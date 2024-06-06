---
layout: load_md
title: The White Circle | Nahamcon 2022 | BABY RSA Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: crypto
challenge: BABY RSA
tags: "crypto, ava, rsa"
date: 2022-05-03T00:00:00+00:00
last_update: 2022-05-03T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2022</h1>

## BABY RSA
> Solved BY : Avantika

We are given a connection to server, where we are presented with some options as follows

![](https://i.imgur.com/1U1K8a3.png)

options do as their description says, we will jump right into the quiz

**Quiz #1** is pretty simple RSA challenge, here is the script to solve that

```python
from Crypto.Util.number import inverse
c = ciphertext
e = e
n = n
#find p and q using factordb.com
p = p
q = q
phi = (p - 1) * (q - 1)
d = inverse(e, phi)
m = pow(c, d, n)
print(m)
```

**Quiz #2** is small e attack, we just need to take cube-root of the cipher-text and we get the plain-text

```python
from decimal import Decimal
import decimal
def cube_root(x):
    return Decimal(x) ** (Decimal(1) / Decimal(3))
with decimal.localcontext() as context:
    context.prec = 150
    print(cube_root(ciphertext))
```

**Quiz #3** is fermat-attack as the primes chosen were too close

```python
import gmpy2
import math
n = n

def fermat_factor(n):
    assert n % 2 != 0

    a = gmpy2.isqrt(n)
    b2 = gmpy2.square(a) - n

    while not gmpy2.is_square(b2):
        a += 1
        b2 = gmpy2.square(a) - n

    p = a + gmpy2.isqrt(b2)
    q = a - gmpy2.isqrt(b2)

    return int(p), int(q)


(p, q) = fermat_factor(n)

print("p = {}".format(p))
print("q = {}".format(q))
```

Solving these two gives us our flag.

