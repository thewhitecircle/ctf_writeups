# crypto

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

----------

## UNIMOD
> Solved By : Avantika

In this challenge we are given the following script along with the cipher text

unimod.py

```python
import random

flag = open('flag.txt', 'r').read()
ct = ''
k = random.randrange(0,0xFFFD)
for c in flag:
    ct += chr((ord(c) + k) % 0xFFFD)

open('out', 'w').write(ct)
```

output.txt
```
饇饍饂饈饜餕饆餗餙饅餒餗饂餗餒饃饄餓饆饂餘餓饅餖饇餚餘餒餔餕餕饆餙餕饇餒餒饞飫
```

Looking at the script we can see that a random integer is being assigned to the variable `k` , to find this integer, we can do a simple trick, as we know that first letter of flag, is going to be `f` i.e 102 in [ASCII chart](https://www.cs.cmu.edu/~pattis/15-1XX/common/handouts/ascii.html) , we can brute-force to find `k`, here is the brute-force script

```python
ct = '饇饍饂饈饜餕饆餗餙饅餒餗饂餗餒饃饄餓饆饂餘餓饅餖饇餚餘餒餔餕餕饆餙餕饇餒餒饞飫'

for k in range(0, 65533):
    goal = ct[0]
    goal = ord(goal)
    num = (102 - k) % 65533
    if goal == num:
        print(k)
        break
```

Script simple takes the first character of cipher-text, gets the ASCII value to and assigns that to `goal`then just “reverses” what the `unimod.py` script did

Next part is pretty easy, we just perform the same operation again on the cipher-text and we would get the flag

```python
k = 26396
for char in ct:
    print(chr((ord(char) + k) % 65533), end='')
```

This prints out our flag.

