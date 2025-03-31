---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | TRIFORCE Writeup
desc: Check out our writeup for TRIFORCE for Hacktivitycon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: crypto
challenge: TRIFORCE
tags: "crypto, choco, aes, encryption, xor"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---


> Solved By : choco

This is a CBC AES encryption (Chained Block Cipher)

Suppose the plain text P is 64 bits long
It is then split into 4 parts (16 bit each)

There is an initializer iv that is 16 bits long and key k (16 bits long)
The cipher text for first part of P is

C0 = E(P0 ^ iv,k)
and following chain goes like this
Ci = E(Pi ^ Ci-1,k) i > 0 


![Cipher block chaining (CBC) mode encryption](https://i.imgur.com/5PtFyrs.png)


The decryption does like this

![Cipher block chaining (CBC) mode decryption](https://i.imgur.com/3KnZgU3.png)


Where P0 = D(C0,k) ^ iv
and following decryption goes
Pi = D(Ci,k) ^ Ci-1

If the key k and vi are same, a vulnerability arises
During decryption,
P0 = D(C0,k) ^ k
P1 = D(C1,k) ^ C0
P2 = D(C2,k) ^ C1

if we replace C2 with C0
P2 = D(C0,k) ^ C1

xor the result with any of the two inputs will give the other input
D(C0,k) = P2 ^ C1
P0 = D(C0,k) ^ k
k = D(C0,k) ^ P0
therefore, k = P2 ^ C1 ^ P0
so since we know P0,P2 and C1, we can easily find k

Hence all we need to do is to replace the 3rd block of ciphertext with the first block of cipher text,
Xor the result of 3rd plain text block with 2nd cipher text block and xor that result with 1st plaintext block to get the key 

Do this three times with the different keys above to get the flag
input: `616161616161616161616161616161616161616161616161616161616161616161616161616161616161616161616161`

Code for replacing ciphertext:


    def listToString(s): 
        str1 = ""   
        for ele in s: 
            str1 += ele   
        return str1 
    
    def chunk(in_string,num_chunks):
        chunk_size = len(in_string)//num_chunks
        if len(in_string) % num_chunks: chunk_size += 1
        iterator = iter(in_string)
        for _ in range(num_chunks):
            accumulator = list()
            for _ in range(chunk_size):
                try: accumulator.append(next(iterator))
                except StopIteration: break
            yield ''.join(accumulator)
            
    string = "e5cc7f05f279ca152d3fbd32d899b55fec8de5d9ae3d62bac51195ddedc0863462021d3c51a4a56555ca95ff4303b764a02a2f44327cb2007d46de117900720a"
    l = list(chunk(string,4))
    l[2] = l[0]
    print(l)
    out = listToString(l)
    print(out)

code for getting hex for part of flag:

    def chunk(in_string,num_chunks):
        chunk_size = len(in_string)//num_chunks
        if len(in_string) % num_chunks: chunk_size += 1
        iterator = iter(in_string)
        for _ in range(num_chunks):
            accumulator = list()
            for _ in range(chunk_size):
                try: accumulator.append(next(iterator))
                except StopIteration: break
            yield ''.join(accumulator)
    
    string = "6227616161616161616161616161616161616161616161616161616161616161e8c6e5dfb46432e2c2499084e899d462e6af4534aed0627d75f825c096970f36"
    l = list(chunk(string,4))
    c = "ec8de5d9ae3d62bac51195ddedc08634"
    
    print(hex(int(l[2],16)))
    
    d = int(l[2],16) ^ int(c,16)
    print(hex(d))
    e = d ^ int(l[0],16)
    print(hex(e))
![](https://i.imgur.com/TgyoAnU.jpg)


 
do this and replace each cipher for each key 
each keys in hex = `666c61677b3831396639643864383337` `32316163346334343262313635396633` `36646632647d20202020202020202020`

flag: `**flag{819f9d8d83721ac4c442b1659f36df2d}**`

