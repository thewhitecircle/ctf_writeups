---
layout: load_md
title: The White Circle | Nahamcon 2021 | Dice Roll Writeup
desc: Check out our writeup for Dice Roll for Nahamcon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: crypto
challenge: Dice Roll
tags: "crypto, rng, python"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---



The given script suggests that it is using random number generators, python uses mersenne twister algorithm, googling we find a github repo of python module which enable us to predict the number.

https://github.com/kmyk/mersenne-twister-predictor


We use the following code, to automate the process using pwntools

```
from pwn import *
from mt19937predictor import MT19937Predictor
import re
predictor = MT19937Predictor()

r = remote('challenge.nahamcon.com' , '31784')
r.recvuntil('> ')
for i in range (0, 625):
        
        r.sendline('2')
        text = r.recvuntil('> ')
        print(text)
        num = [int(s) for s in text.split() if s.isdigit()]
        print(num)
        num = num[0]
        predictor.setrandbits(num, 32)

flag = predictor.getrandbits(32)
r.sendline('3')
print(r.recvuntil('> '))
r.sendline(str(flag))
print(r.recv(2048))
```

