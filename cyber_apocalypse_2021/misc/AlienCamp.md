---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Alien Camp Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: misc
challenge: Alien Camp
tags: "misc, twh, python, scripting"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Alien Camp

> Solved By : thewhiteh4t

* The challenge server sends a randomized set of emoji and value pair
* We can get all pairs from option 1
* After getting all pairs we can start calculating values for each question
* If you get EOF Error just restart the script
* After 500 questions we get the flag

```python
    #!/usr/bin/env python3
    
    from pwn import *
    
    host = '46.101.82.40'
    port = 32156
    conn = remote(host, port)
    conn.recvuntil('>').decode()
    print('[+] Getting some help...')
    conn.send('1\n')
    out = conn.recvuntil('\n>').decode()
    help_arr = out.split('\n')
    help_txt = help_arr[2]
    store = help_txt.split(' ')
    emojis = []
    vals = []
    new_store = {}
    counter = 0
    for item in store[0::3]:
        if len(item) > 0:
            emojis.append(item)
    for item in store [2::3]:
        vals.append(item)
    for emo in emojis:
        new_store[emo] = vals[emojis.index(emo)]
    if len(new_store) != 0:
        print('[+] Help recieved!')
    conn.send('2\n')
    def solve(conn):
        global counter
        if counter != 500:
            q_txt = conn.recvuntil('?').decode()
            q_txt = q_txt.split('\n')
            q_txt = q_txt[-1]
            print(f'[Q:{counter}] {q_txt}')
            q_txt = q_txt.split('  =')
            q_txt = q_txt[0]
            q_emo = q_txt.split(' ')
            for elem in q_emo:
                if elem in new_store:
                    q_txt = q_txt.replace(elem, new_store[elem])
            ans = eval(q_txt)
            print(f'[+] Sending {ans}')
            conn.send(str(ans) + '\n')
        else:
            flag = conn.recvuntil('}').decode()
            print(flag)
            conn.close()
        counter += 1
    while counter <= 500:
        solve(conn)
```

