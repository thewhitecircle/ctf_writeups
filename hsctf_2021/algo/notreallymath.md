---
layout: load_md
title: The White Circle | Hsctf 2021 | not-really-math Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hsctf 2021
parent: hsctf_2021
category: algo
challenge: not-really-math
tags: "algo, ava, taz, python, scripting"
date: 2021-06-20T00:00:00+00:00
last_update: 2021-06-20T00:00:00+00:00
---

<h1 class="heading card-title white-text">Hsctf 2021</h1>

## not-really-math
> Solved by: nsa-bot and taz34

```
nc not-really-math.hsc.tf 1337
```

I did this manually by passing the values we get through this script one by one.

```python
import math
from pwn import *
question = input("Enter Here: ")
question = question.replace('m', '*')
question = question.replace('a', '+')
question = question.split('*')
problem = []
for i in question:
    problem.append(eval(i))
answer = math.prod(problem)
if answer > 99999:
    print(answer%4294967295)
else:
    print(answer)
```

**This is just the concept** for understanding, we can solve it using this by passing each value we get through this and the copying back the answer we get from here to the script, but that is very time consuming.

**nsa-bot** wrote an awesome script to automate the process process:

```python
import math
from pwn import *
from re import search
r = remote('not-really-math.hsc.tf',  1337)
    
data = r.recvuntil('\n: ')
data = data.decode().split('\n')
question = data[1]
question = question.replace('m', '*')
question = question.replace('a', '+')
question = question.split('*')
problem = []
for i in question:
    problem.append(eval(i))
answer = math.prod(problem)
r.sendline(str(answer))
    
flagfound = False
while flagfound == False:
    try:
        data = r.recvuntil('\n: ')
    except EOFError:
        data = r.recv()
    data = data.decode().split('\n')
    if search('flag', str(data)):
        flagfound = True
        flag = data[0]
        print('flag is ', flag)    
    else:
        question = data[0]
        question = question.replace('m', '*')
        question = question.replace('a', '+')
        question = question.split('*')
        problem = []
        for i in question:
            problem.append(eval(i))
        print('[+]Solving Question')
        answer = math.prod(problem)
        answer = answer%4294967295
        r.sendline(str(answer))
        print('[+]Sending Answer')
        flagfound = False
```

![](https://i.imgur.com/Id6cKFI.png)

```
flag{yknow_wh4t_3ls3_is_n0t_real1y_math?_c00l_m4th_games.com}
```