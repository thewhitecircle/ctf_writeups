# Cryptography

## esab64 

Its base64 backwards

initial string in file: `mxWYntnZiVjMxEjY0kDOhZWZ4cjYxIGZwQmY2ATMxEzNlFjNl13X`

The name is backwards so i reversed the string to:

`X31lNjFlNzExMTA2YmQwZGIxYjc4ZWZhODk0YjExMjViZntnYWxm`
 
base64 decode to: `_}e61e711106bd0db1b78efa894b1125bf{galf`
 
reverse the string once again for flag: `flag{fb5211b498afe87b1bd0db601117e16e}_`

----------

## chicken wings

WingDing Cipher : https://lingojam.com/WingDing

----------

## veebee

Visual Basic Script can be encoded and they become vbe files, there are some encoders in the wild, python one did not work but there is another decoder here which works : https://www.interclasse.com/scripts/decovbe.php

* put the code in a .vbs file
* launch a cmd and execute

```
wscript decode.vbs veebee.vbe
```

* a dialogue box will open with the flag in it

----------

## Dice Roll

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

---

## Car Keys:
keyed caesar cipher

https://www.boxentriq.com/code-breaking/keyed-caesar-cipher

use the key: `QWERTY`

---

## Treasure

* title is `treasure`
* if you search for `treasure` cipher you will land on beale cipher
* and eventually, book cipher

https://www.dcode.fr/book-cipher

![](https://i.imgur.com/hV42xOk.png)

----------

## EAXY

Brute force the text, by uploading the file
get the output search for `XOR KEY` you get plaintext, thankie to @twh 
write down the keys like this with indexes and then join them to get the string then convert it to hex, boom!

```
key 30 -32
key = 31 - 5, 14, 21
key = 32 - 15, 23, 
key 33 - 18, 
key 34 - 16, 17
key 35 - 12
key 36 - 6, 20, 27
key 37 - 35
key 38 - 22, 24
key 39 - 31, 36
key 61 - 2, 25, 29
key 62 - 19, 30
key 63 - 10, 13, 28, 34
key 64 - 8, 33
key 65 - 7, 11
key 66 - 0, 9, 26
key 67 - 3
key 6c - 1
key 7b - 4
key 7d - 37
key 30 - 32
key 31 - 5, 14, 21
key 32 - 15, 23

666c61677b31366564666365356331323434336236313832386166366361623930646337397d
convert above string to hex to get the flag = `flag{16edfce5c12443b61828af6cab90dc79}`
```
