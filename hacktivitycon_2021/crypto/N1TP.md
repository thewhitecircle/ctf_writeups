---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | N1TP Writeup
desc: Check out our writeup for N1TP for Hacktivitycon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: crypto
challenge: N1TP
tags: "crypto, choco, python"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---


> Solved By : choco

We are given an encrypted flag with randomized key.
The hint is that this uses a one time pad encryption

![](https://i.imgur.com/vxsQdmu.png)


The general gist of one pad encryption:
You have a plain text: P = “HELLO” (8 5 12 12 15)
We are given a key: K = “DREFX” (4 18 5 6 24)

The encryption comes in when we add both P and K to get encryption C = “L W Q R M” (12 23 17 18 13(39%26))

So P + K = (C%N) where N is the letter range
To get the decipher text we simple subtract C with K
P = “HELLO” (8 5 12 12 15(11%26)

So (P%N) = C - K

The vulnerability comes in if the same key K is used to encrypt another plain text provided we know the range of text used along with the length

Suppose P1 = “AAAAA” (1 1 1 1 1)
P1 + K = C1
C1 = “ETFGY” (5 19 6 7 25)

We could easily get the key if it is within the modulus range
C1 - P1 = K
K = “DREFX” (4 18 5 6 24)

We can now use this to get P with the given C

But another way to find P is using simple logic

C1 - P1 = C - P
So P = C - C1 + P1
All we need to do is 
find C - C1 to 0 and we can guess P 

Using this logic we will find the flag 
The code for difference C - C1 is given below and difference is added to P1
and manually repeated until we get C - C1 as 0


    import binascii
    
    A = "dc0de91facbe90ee6f652167906ee0d17123cf9e746a63db4b4e7d93040f59331ead9be0b2fe"
    Ahex = binascii.unhexlify(A) 
    B = "dc0de91facbe90ee6f652167906ee0d17123cf9e746a63db4b4e7d93040f59331ead9be0b2fe"
    
    Al = list(bytearray(Ahex))
    Bl = list(bytearray(binascii.unhexlify(B)))
    C = "flag{00000000000000000000000000000000}"
    
    Chex = list(bytearray(C))
    print(Chex)
    
    d = []
    for i in range(len(Al)):
        d.append(Al[i] - Bl[i])
    print(d)
    
    k = []
    c = []
    for i in range(len(Al)):
        k.append(Chex[i] + d[i])
        if((48 <= k[i] <= 57) or (97 <= k[i] <= 102) or k[i] == 108 or k[i]==103 or k[i]==123 or k[i]==125):
                c.append(chr(k[i]))
        else:
                c.append("#")
    print(k)
    print(c)


![](https://i.imgur.com/LIE6yxg.png)


flag: `flag{9276cdb76a3dd6b1f523209cd9c0a11b}`