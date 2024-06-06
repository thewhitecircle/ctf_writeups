---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Stop Drop and Roll Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: misc
challenge: Stop Drop and Roll
tags: "misc, legend, python, scripting"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

## Stop Drop and Roll
> Solved by : Legend

Challenge description:

```
The Fray: The Video Game is one of the greatest hits of the last... well, we don't remember quite how long. Our "computers" these days can't run much more than that, and it has a tendency to get repetitive...
```

This challenge is playing a game in which we have to input based on the challenge question.
For example if it says `GORGE` we need to respond `STOP`, if `GORGE, FIRE` we need to send `STOP-ROLL` and so on till we finally get the flag.


```python
    #! /usr/bin/python3
    from pwn import *
    
    IP_ADDRESS = '83.136.252.248'
    PORT = 54677
    
    conn = remote(IP_ADDRESS, PORT)
    
    print(conn.recvuntil(b'(y/n)').decode())
    
    conn.sendline(b'y')
    
    print(conn.recvline().decode())
    
    responses = {
        'GORGE': 'STOP',
        'PHREAK': 'DROP',
        'FIRE': 'ROLL'
    }
    
    while True:
        prompt = conn.recvline().decode().strip()
        print(prompt)
    
        if "What do you do?" in prompt:
            keywords = prompt.split("What do you do?")[1].strip().split(', ')
        else:
            keywords = prompt.split(', ')
        
        response_keywords = [responses.get(keyword) for keyword in keywords]
    
        if None in response_keywords:
            break
    
        response = '-'.join(response_keywords)
        
        conn.sendline(response.encode())
        print(f"Response: {response}")
        
        if 'Unfortunate! You died!' in prompt:
            break
    
        if 'Fantastic work! The flag is' in prompt:
            flag = prompt.split('Fantastic work! The flag is')[1].strip()
            print(f"The flag is: {flag}")
            break
    
    conn.close()
```

```
The flag is: HTB{1_wiLl_sT0p_dR0p_4nD_r0Ll_mY_w4Y_oUt!}
```

