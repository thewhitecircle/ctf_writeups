---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Dynastic Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: crypto
challenge: Dynastic
tags: "crypto, starry, python, encryption"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

## Dynastic
> Solved by : Starry-lord

This challenge comes with an output.txt and a python source as you can see below:

```python
    from secret import FLAG
    from random import randint
    
    def to_identity_map(a):
        return ord(a) - 0x41
    
    def from_identity_map(a):
        return chr(a % 26 + 0x41)
    
    def encrypt(m):
        c = ''
        for i in range(len(m)):
            ch = m[i]
            if not ch.isalpha():
                ech = ch
            else:
                chi = to_identity_map(ch)
                ech = from_identity_map(chi + i)
            c += ech
        return c
    
    with open('output.txt', 'w') as f:
        f.write('Make sure you wrap the decrypted text with the HTB flag format :-]\n')
        f.write(encrypt(FLAG))
```

We needed to write a decryption mechanism for this, taking the output file as input. Here’s my solution:


![](https://i.imgur.com/bxRTrzW.png)


```
HTB{DID_YOU_KNOW_ABOUT_THE_TRITHEMIUS_CIPHER?!_IT_IS_SIMILAR_TO_CAESAR_CIPHER}
```

