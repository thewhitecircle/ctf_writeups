---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Primary Knowledge Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: crypto
challenge: Primary Knowledge
tags: "crypto, legend, rsa, python"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

## Primary Knowledge 
> Solved by: Legend

Challenge description :

```
Surrounded by an untamed forest and the serene waters of the Primus river, your sole objective is surviving for 24 hours. Yet, survival is far from guaranteed as the area is full of Rattlesnakes, Spiders and Alligators and the weather fluctuates unpredictably, shifting from scorching heat to torrential downpours with each passing hour. Threat is compounded by the existence of a virtual circle which shrinks every minute that passes. Anything caught beyond its bounds, is consumed by flames, leaving only ashes in its wake. As the time sleeps away, you need to prioritise your actions secure your surviving tools. Every decision becomes a matter of life and death. Will you focus on securing a shelter to sleep, protect yourself against the dangers of the wilderness, or seek out means of navigating the Primus’ waters?
```

We are given the source code which is of `RSA` algorithm and along with that we are given the output file with the value of `n`, `c`, and `e`.

To solve this I used http://www.factordb.com/ to get the value of `p` and `q` and wrote the script.

```python
    #! /usr/bin/python3
    
    import gmpy2
    import binascii
    
    n = 144595784022187052238125262458232959109987136704231245881870735843030914418780422519197073054193003090872912033596512666042758783502695953159051463566278382720140120749528617388336646147072604310690631290350467553484062369903150007357049541933018919332888376075574412714397536728967816658337874664379646535347
    e = 65537
    c = 15114190905253542247495696649766224943647565245575793033722173362381895081574269185793855569028304967185492350704248662115269163914175084627211079781200695659317523835901228170250632843476020488370822347715086086989906717932813405479321939826364601353394090531331666739056025477042690259429336665430591623215
    p = 144595784022187052238125262458232959109987136704231245881870735843030914418780422519197073054193003090872912033596512666042758783502695953159051463566278382720140120749528617388336646147072604310690631290350467553484062369903150007357049541933018919332888376075574412714397536728967816658337874664379646535347
    q = 144595784022187052238125262458232959109987136704231245881870735843030914418780422519197073054193003090872912033596512666042758783502695953159051463566278382720140120749528617388336646147072604310690631290350467553484062369903150007357049541933018919332888376075574412714397536728967816658337874664379646535347
    
    phi = (p-1)*(q-1)
    d = gmpy2.invert(e,phi)
    m = gmpy2.powmod(c,d,n)
    
    flag = (binascii.unhexlify(hex(m)[2:])).decode()
    
    print(f"Flag: {flag}")
```

```
Flag: HTB{0h_d4mn_4ny7h1ng_r41s3d_t0_0_1s_1!!!}
```

