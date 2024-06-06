---
layout: load_md
title: The White Circle | M0Lecon 2021 | Lucky Fall Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: M0Lecon 2021
parent: m0lecon_2021
category: web
challenge: Lucky Fall
tags: "web, twh"
date: 2021-05-17T00:00:00+00:00
last_modified_at: 2021-05-17T00:00:00+00:00
---

<h1 class="heading card-title white-text">M0Lecon 2021</h1>

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

