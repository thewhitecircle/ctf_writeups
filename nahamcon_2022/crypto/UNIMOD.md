---
layout: load_md
title: The White Circle | Nahamcon 2022 | UNIMOD Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: crypto
challenge: UNIMOD
tags: "crypto, ava, python"
---

<h1 class="heading card-title white-text">Nahamcon 2022</h1>

## UNIMOD
> Solved By : Avantika

In this challenge we are given the following script along with the cipher text

unimod.py

```python
import random

flag = open('flag.txt', 'r').read()
ct = ''
k = random.randrange(0,0xFFFD)
for c in flag:
    ct += chr((ord(c) + k) % 0xFFFD)

open('out', 'w').write(ct)
```

output.txt
```
饇饍饂饈饜餕饆餗餙饅餒餗饂餗餒饃饄餓饆饂餘餓饅餖饇餚餘餒餔餕餕饆餙餕饇餒餒饞飫
```

Looking at the script we can see that a random integer is being assigned to the variable `k` , to find this integer, we can do a simple trick, as we know that first letter of flag, is going to be `f` i.e 102 in [ASCII chart](https://www.cs.cmu.edu/~pattis/15-1XX/common/handouts/ascii.html) , we can brute-force to find `k`, here is the brute-force script

```python
ct = '饇饍饂饈饜餕饆餗餙饅餒餗饂餗餒饃饄餓饆饂餘餓饅餖饇餚餘餒餔餕餕饆餙餕饇餒餒饞飫'

for k in range(0, 65533):
    goal = ct[0]
    goal = ord(goal)
    num = (102 - k) % 65533
    if goal == num:
        print(k)
        break
```

Script simple takes the first character of cipher-text, gets the ASCII value to and assigns that to `goal`then just “reverses” what the `unimod.py` script did

Next part is pretty easy, we just perform the same operation again on the cipher-text and we would get the flag

```python
k = 26396
for char in ct:
    print(chr((ord(char) + k) % 65533), end='')
```

This prints out our flag.

