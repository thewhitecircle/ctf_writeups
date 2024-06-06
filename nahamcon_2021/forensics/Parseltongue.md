---
layout: load_md
title: The White Circle | Nahamcon 2021 | Parseltongue Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: forensics
challenge: Parseltongue
tags: "forensics, python, bytecode"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## Parseltongue

first get python code from bytecode using `uncompyle6`

```
    #!/usr/bin/env python3
    import Crypto.Util.number as l2b
    import random
    '''
        sszz -> a_list
        zzss -> some_bytes
    '''
    a_list = [
     'aposlogahs', 'apsle', 'Sine', 'aʃe', 'bei∫ed', 'tuif', 'Kura', 'Vera', 'pard', 'pardshesl', 'bo∫', 'Gara', 'vinth', 'Pelʃis', 'keilsing', 'khair', 'tikni', 'Bana', 'Slehara', 'koukh', 'kups', 'dai', 'Andi', 'dorʃe', 'doʃe', 'sloʃe', 'kaʃe', 'Sarna', 'Suu', 'giʃe', 'Gorna', 'ass-girou', 'dros', 'feslure', 'hasli', 'riʃan', 'fraeslis', 'vris', 'gatsi', 'runʃe', 'Tira', 'hishe', 'einʃe', 'hesleuf', 'Firna', 'Baʃ', 'ʃem', 'ai', 'ine', 'dinʃe', 'Negei', 'slanp', 'ʃena', 'sliʃe', 'dati', 'slifai', 'Kuine', 'Ha', 'nisl', 'ʃe', 'Sobne', 'bna', 'Sora', 'ovith', 'houk', 'parknent', 'fasar', 'nesha', 'praughs', 'Pura', 'ʃine', 'ʃane', 'gisan', 'rai∫e', 'kata', 'Ara', 'Nigi', 'akaʃe', 'rashe', 'slan', 'Derne', 'Tina', 'snart', 'gariʃe', 'kerashe', 'stabsle', 'Fasi', 'Peina', 'Tasi', 'Sekusi', 'Harne', 'kapi', 'Athne', 'vaʃe', 'asl', 'ʃik', 'agiro', 'vei', 'Asuna', 'Teʃ', 'Fiʃ', 'Doʃ', 'ʃira', 'Haʃ', 'Vuʃ', 'vindovth', 'Bira', 'Sa', 'Slu', 'ou', 'iangsteur'
    ]
    some_bytes = b'\x07\x1c\x0e\x14\x17\n\x06\x03\x0cJ\x00@G\x0e\x017X\x0b\x04W\xf8\xb5\x03P\x06\x0f\x80\xea\x9b\x00\x05A\x16\\\x00.\x17\x0f'
    s = False
    z = True
    ss = s & z  # False AND True -> always False
    # abs()      -> Returns absolute value
    # abs(True)  -> 1
    # abs(False) -> 0
    z = abs(ss) - abs(z) # -1
    zz = ss | z          # False OR -1 = -1
    z = zz - z - z       # -1 - (-1) - (-1) = 1
    zz = z | z           # 1 OR 1 = 1
    z = zz << zz         # 1 LEFT SHIFT 1 = 2
    s = ss >> ss         # False RIGHT SHIFT False = 0
    sz = s << z          # 0 LEFT SHIFT 2 = 0
    zs = z << s          # 2 LEFT SHIFT 0 = 2
    z = zs - sz          # 2 - 0 = 2
    # Values at this point
    # z  = 2
    # zs = 2
    # sz = 0
    # s  = 0
    # zz = 1
    # ss = False [unchanged]
    ss = str(z).replace(str(zs), str(ss).replace(str(ss), str(z).replace(str(z), '')))
    # '2'.replace('2', 'False'.replace('False', '2'.replace('2', '') ))
    # '2'.replace('2', 'False'.replace('False', ''))
    # '2'.replace('2', '')
    # ss = ''
    sss = bytes(ss.join(a_list), 'utf-8')
    zzz = bytes([_a ^ _b for _a, _b in zip(sss, some_bytes)])
    ##### JACKPOT #####
    print(zzz.decode())
    ###################
    ssszzz = bytes([_a ^ _b for _a, _b in zip(zzz, some_bytes)])
    sss += b'S'
    ssss = []
    ss = sss[:len(sss) // 2]
    zz = sss[len(sss) // 2:]
    for s in range(len(ss)):
        ssss.append(ss[s] ^ zz[s])
    else:
        if 5 == 1:
            print(' '.join([random.choice(a_list).upper() for _ in range(random.randrange(5, 10))]))
        else:
            print(' '.join([random.choice(a_list).upper() for _ in range(random.randrange(5, 10))]))
```
