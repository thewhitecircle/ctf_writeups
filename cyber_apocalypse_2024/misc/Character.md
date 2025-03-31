---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Character Writeup
desc: Check out our writeup for Character for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: misc
challenge: Character
tags: "misc, legend, python, scripting"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by : Legend

Challenge description:

```
Security through Induced Boredom is a personal favourite approach of mine. Not as exciting as something like The Fray, but I love making it as tedious as possible to see my secrets, so you can only get one character at a time!
```

In this challenge the server is giving us the flag if we put the index value. For example if I put `0` then it will print the value `H` and so on. We need to do this till we get the complete flag till `}` character.

```python
    #! /usr/bin/python3
    
    from pwn import *
    
    SERVER_IP = "94.237.53.53"
    SERVER_PORT = 47157
    
    conn = remote(SERVER_IP, SERVER_PORT)
    
    flag = ""
    
    for index in range(111):
        conn.sendline(str(index).encode('ascii'))
    
        server_response = conn.recvline().decode().strip()
    
        character = server_response.split()[-1]
        flag += character
    
        print(f"Index {index} : Character {character}", end="\r", flush=True)
        
        if character == "}":
            break
    
    print(f"Flag: {flag}")
    
    conn.close()
```

```
Flag: HTB{tH15_1s_4_r3aLly_l0nG_fL4g_i_h0p3_f0r_y0Ur_s4k3_tH4t_y0U_sCr1pTEd_tH1s_oR_els3_iT_t0oK_qU1t3_l0ng!!}
```

