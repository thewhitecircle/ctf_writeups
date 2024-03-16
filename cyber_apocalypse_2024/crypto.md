# crypto

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

```Flag: HTB{0h_d4mn_4ny7h1ng_r41s3d_t0_0_1s_1!!!}```

---

## Makeshift
> Solved by: Legend

Challenge description:

```
Weak and starved, you struggle to plod on. Food is a commodity at this stage, but you can’t lose your alertness - to do so would spell death. You realise that to survive you will need a weapon, both to kill and to hunt, but the field is bare of stones. As you drop your body to the floor, something sharp sticks out of the undergrowth and into your thigh. As you grab a hold and pull it out, you realise it’s a long stick; not the finest of weapons, but once sharpened could be the difference between dying of hunger and dying with honour in combat.
```

For this challenge we are given the code in which the flag is just getting shifted. So I just reversed the script with trail and error I got the flag.

```python
    #! /usr/bin/python3
    
    new_flag = "!?}De!e3d_5n_nipaOw_3eTR3bt4{_THB"
    
    flag = ''
    
    for i in range(0, len(new_flag), 3):
        flag += new_flag[i+2]
        flag += new_flag[i]
        flag += new_flag[i+1]
    
    flag = flag[::-1]
    
    print(flag)
```

```HTB{4_b3tTeR_w3apOn_i5_n3edeD!?!}```

---

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


```HTB{DID_YOU_KNOW_ABOUT_THE_TRITHEMIUS_CIPHER?!_IT_IS_SIMILAR_TO_CAESAR_CIPHER}```

