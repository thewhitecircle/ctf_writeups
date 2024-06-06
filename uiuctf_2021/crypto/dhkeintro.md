---
layout: load_md
title: The White Circle | Uiuctf 2021 | dhke_intro Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Uiuctf 2021
parent: uiuctf_2021
category: crypto
challenge: dhke_intro
tags: "crypto, choco"
date: 2021-08-12T00:00:00+00:00
last_modified_at: 2021-08-12T00:00:00+00:00
---

<h1 class="heading card-title white-text">Uiuctf 2021</h1>

## dhke_intro
> Solved by : choco

“Small numbers are bad in cryptography. This is why.”

This is a DK cipher with just 28 possible keys to find
For a given p and g from the random list, even if the generated k is randomized and dependant on p and g, we know that k should be lesser than p because of modulus operation


    gpList = [ [13, 19], [7, 17], [3, 31], [13, 19], [17, 23], [2, 29] ]
    g, p = random.choice(gpList)
    a = random.randint(1, p)
    b = random.randint(1, p)
    k = pow(g, a * b, p)
    k = str(k)

**since the maximum value of p is 29, k is from 0 to 28**

the padded key is also predictable 

    padding = "uiuctf2021uiuctf2021"
    while (16 - len(key) != len(k)):
        key = key + padding[i]
        i += 1
    key = key + k
    key = bytes(key, encoding='ascii')

so **key can be from b'uiuctf2021uiuct0' to b'uiuctf2021uiuct9' and b'uiuctf2021uiuc10' to b'uiuctf2021uiuc28'**

the VI is fixed, so with the small possible set of keys and a fixed VI we can easily bruteforce into getting the decrypted flag from the dk cipher


    import binascii
    from Crypto.Cipher import AES
    cf = "b31699d587f7daf8f6b23b30cfee0edca5d6a3594cd53e1646b9e72de6fc44fe7ad40f0ea6"
    unhcf = bytes.fromhex(cf)
    for k in range(0,28):
            k = str(k)
            key = ""
            i = 0
            padding = "uiuctf2021uiuctf2021"
            while (16 - len(key) != len(k)):
                key = key + padding[i]
                i += 1
            key = key + k
            key = bytes(key, encoding='ascii')
            iv = bytes("kono DIO daaaaaa", encoding = 'ascii')
            cipher = AES.new(key, AES.MODE_CFB, iv)
            flag = cipher.decrypt(unhcf)
            try:
                    print(flag.decode("ASCII"))
                    print(key)
            except:
                    i = 0

**key:  b'uiuctf2021uiuct9'**

```flag: uiuctf{omae_ha_mou_shindeiru_b9e5f9}```

