---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | PhaseStream 1 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: crypto
challenge: PhaseStream 1
tags: "crypto, legend, xor, python"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## PhaseStream 1

> Solved by: Legend

* Each character will be XOR with each character of the key and the length of the key is 5 characters.

```python
    start =  bytearray.fromhex('2e313f2702')  #Randomly tried from starting of given cipher
    text = 'CHTB{'.encode()
    
    key = b''
    output = b''
    
    for i in range(len(start)):
            key += bytes([text[i] ^ start[i]])
    
    print('Key: ' + str(key))
    print('Key len: '+ str(len(key)))
    print('Key type: ' + str(type(key)))
    print('Key hex: ' + key.hex())
    
    for i in range(int(len(start))):
            output += bytes([start[i] ^ key[i % len(key)]])
    
    print('2e313f2702 --> '+ '(' + output.hex() + ')' + ' == ' + str(output) + '(text)')
    
    shifr  = bytearray.fromhex('2e313f2702184c5a0b1e321205550e03261b094d5c171f56011904')
    
    output2 = b''
    
    for i in range(len(shifr)):
            output2 += bytes([shifr[i] ^ key[i % len(key)]])
    
    output_dec = output2.decode(errors='ignore')
    
    print('Flag: '+ output_dec)
    print('Flag hex: '+ output.hex())

Flag: CHTB{u51ng_kn0wn_pl41nt3xt}
```

