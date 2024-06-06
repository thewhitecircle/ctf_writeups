---
layout: load_md
title: The White Circle | M0Lecon 2021 | M0leFans Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: M0Lecon 2021
parent: m0lecon_2021
category: web
challenge: M0leFans
tags: "web, twh, file upload, xss"
date: 2021-05-17T00:00:00+00:00
last_update: 2021-05-17T00:00:00+00:00
---

<h1 class="heading card-title white-text">M0Lecon 2021</h1>

## M0leFans
> Solved By : thewhiteh4t

This challenge begins at a login/register page, after login we can see the following :

- public feed
- profile settings
- report link
- create post button
- search
- copy profile link
- followers

![](https://i.imgur.com/Dta95kx.png)

Inside settings we can set our custom subdomain and we can upload an avatar as well. I tried lot of XSS payloads in name fields as well as create post but JS was not executing, instead it was being printed on the page like normal text so I tried file upload instead

Only images are supported in the file upload but it can be easily bypassed with a GIF header or any other image header

```
GIF header -> GIF89a;
```

After trying different file formats for upload I noticed that files were not executing at all, instead they were being downloaded as text files with a randomized filename, so eventually I tried an HTML file with a simple JS alert pop up and it worked!

File upload : 

![](https://i.imgur.com/w1mVfzK.png)

In inspect element we can see that a HTML file has appeared in `src` of `img` tag :

![](https://i.imgur.com/NdzDLgV.png)

and the JS code is working!

![](https://i.imgur.com/obQ9nEi.png)

Now I saw the report link at the bottom of the page and it mentions that we can report posts to admin, at this point I thought we need to steal the cookie of admin and get access to the admin account but when I checked the cookie it had `HttpOnly`  flag enabled so this was not the correct direction.

I read the challenge description again and it mentions about `following` people, now I thought if somehow I can make the admin follow my account then I will be able to get the flag so I started looking into the `follow` request

![](https://i.imgur.com/K2BgOiQ.png)

It is a simple GET request on `/profile/follow` . Since JS is working I made a small JS script to call this request.

In theory it works like this :

1. Upload new HTML file with updated JS script
2. Get the HTML file link from page source
3. Report the link
4. Admin opens the link and since JS will automatically execute the admin will silently follow us

HTML code :

```html
GIF89a;
<html>
<head></head>
<body>
    <script>
        var xhttp = new XMLHttpRequest();
        xhttp.open("GET", "https://twc.m0lecon.fans/profile/follow", true);
        xhttp.withCredentials = true;
        xhttp.send();
    </script>
</body>
</html>
```

I got a success message after submitting the link

![](https://i.imgur.com/O3y4LjU.png)

And on the followers page I got a new request!

![](https://i.imgur.com/OFsnhsW.png)

This is the admin, I accepted the request but nothing happens, I cant see the profile of admin there is no link provided. To access the admin profile I needed a subdomain because every profile gets its own unique subdomain. I tried to guess it but that did not work, then I poked around the site for some time but could not find anything which revealed the subdomain so I used a classic technique, `subdomain enumeration` :

```
finalrecon --sub https://m0lecon.fans
```

![](https://i.imgur.com/sjgHZUy.png)

and that’s how I found the required subdomain!

```
y0urmuchb3l0v3d4dm1n.m0lecon.fans
```

But when I visited the profile, there is again nothing, the profile is private and bio does not have the flag, but there is a follow button!

![](https://i.imgur.com/OxbCBUZ.png)

When I clicked the follow button it showed request pending!

![](https://i.imgur.com/nxBijew.png)

I waited for some time thinking admin will automatically accept the request but that did not happen so that reminded me that maybe I can make the admin accept my follow request just like before!

The flow was similar to last time but this time a POST request was being sent to `/profile/requests` with a body which contained the `id` of the user who has sent the friend request.

Now I started looking for my id when i noticed that the session cookie is JWT. I decoded the JWT and got the id!

![](https://i.imgur.com/0hP8whT.png)

After compiling the information above here is the new HTML code :

```html
GIF89a;
<html>
<head></head>
<body>
    <script>
        var xhttp = new XMLHttpRequest();
        xhttp.open("POST", "https://y0urmuchb3l0v3d4dm1n.m0lecon.fans/profile/request", true);
        xhttp.withCredentials = true;
        xhttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
        xhttp.send("id=30");
    </script>
</body>
</html>
```

The upload and report were successful just like before and now the admin accepted our follow request!

![](https://i.imgur.com/fVwgb9v.png)

and the fan post contains our beloved flag!

![](https://i.imgur.com/GTPqIKb.png)