---
layout: load_md
title: The White Circle | Nahamcon 2025 | Flagdle Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: misc
challenge: Flagdle
tags: "misc, twh, python"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>

## Flagdle
> Solved by thewhiteh4t


![](https://i.imgur.com/PQRJN6b.png)


In this challenge we were supposed to guess the flag. Like in the game wordle the application highlights which character of the flag are on the correct position and which are not along with incorrect characters.

Here is the response of the example flag in the image above : 


![](https://i.imgur.com/u1Nhikd.png)


To guess and extract the entire flag we can automate the process using python : 


    #! /usr/bin/env python3
    
    import json
    import string
    import requests
    
    url = 'http://challenge.nahamcon.com:30129/guess'
    
    charset = []
    charset.extend(list(string.ascii_lowercase))
    charset.extend(list(string.digits))
    
    flag_chars = ['a' for i in range(32)]
    
    flag = 'flag{' + "".join(flag_chars) + '}'
    
    payload = {
            'guess': flag
    }
    
    rqst = requests.post(url, json=payload)
    
    if rqst.status_code == 200:
            output = list(json.loads(rqst.text)['result'])
            emo_id = 0
            for emoji in output:
                    if emoji != '🟩':
                            for char in charset:
                                    flag_chars[emo_id] = char
                                    flag = 'flag{' + "".join(flag_chars) + '}'
                                    payload = {
                                            'guess': flag
                                    }
                                    sub_rqst = requests.post(url, json=payload)
                                    sub_output = list(json.loads(sub_rqst.text)['result'])
                                    if sub_output[emo_id] == '🟩':
                                            flag = 'flag{' + "".join(flag_chars) + '}'
                                            print(flag)
                                            break
                    emo_id += 1
    else:
            print('Status :', rqst.status_code)
            print('Error  :', rqst.text)
    

This script can be improved in multiple ways to cut down the time and complexity and possibly reduce number of requests sent to the server, but I used the longer method because it's a security CTF and not a coding competition 🙂 

Below you can see the flag gets populated with correct characters on each position. I started with a simple 32 times a flag.


![](https://i.imgur.com/yGBkrnj.png)


**Key Learning and Takeaways**


- Automation : This is exactly the kind of repetitive, pattern-based task where scripting is useful. Trying to guess character by character, position by position, by hand would take forever.
- Leveraging Feedback Loops : We used the game's own feedback against it. Every time we sent a guess, we got information. In real world many business logic bugs can be exploited in similar manner.


