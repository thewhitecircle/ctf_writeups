---
layout: load_md
title: The White Circle | Nahamcon 2021 | EAXY Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: crypto
challenge: EAXY
tags: "crypto, xor"
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## EAXY

We are given a hex like looking data, we upload the file to CyberChef,
From the challenge name we get a hint that it might be XOR, so we try to bruteforce XOR.
For most of keys you get a plaintext in which they have mentioned the index of the each character
for example,The word `HELLO` , here the letter `H` will have index 0, and letter `E` will have 1 and so on.
we write down all the indexes 
Assemble them accordingly and we get the following string
`666c61677b31366564666365356331323434336236313832386166366361623930646337397d`
Which is just a hex string, unhex it and we get the flag 
`flag{16edfce5c12443b61828af6cab90dc79}`
```
KEY    | INDEX
key 30 - 32
key 31 - 5, 14, 21
key 32 - 15, 23, 
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

```
