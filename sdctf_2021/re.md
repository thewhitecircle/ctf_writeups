# revenge

## A bowl of pythons
> Solved by : thewhiteh4t

* In this challenge we just had to understand what the code is doing and make a reverse function for it
* our flag is in this line of code :

```python
if __name__ == "__main__":
    e(b't2q}*\x7f&n[5V\xb42a\x7f3\xac\x87\xe6\xb4')
```

* there are two conditions in function `e` :
    * first condition checks if the first 6 characters of the input are `sdctf{` or not
    * second condition checks if the last character is `}`
* then `e` function stores the content inside `{}` in a variable `g`
* then a XOR operation is performed on each character present in `g`
* the reverse of XOR is XOR
* so I just created a `solve` function to perform XOR on the encoded flag string

```python
#! /usr/bin/env python3

FLAG = 'sdctf{a_v3ry_s3cur3_w4y_t0_st0r3_ur_FLAG}' # lol

a = lambda n: a(n-2) + a(n-1) if n >= 2 else (2 if n == 0 else 1)
b = lambda x: bytes.fromhex(x).decode()
h = eval(b('7072696e74')) # print

def d():
    print('Incorrect flag! You need to hack deeper...')
    eval(__import__("sys").exit(1))
    h(FLAG)

def e(f):
    h("Welcome to SDCTF's the first Reverse Engineering challenge.")

    c = input("Input the correct flag: ")
    if c[:6].encode().hex() != '{2}3{0}{1}{0}3{2}{1}{0}{0}{2}b'.format(*map(str, [6, 4, 7])): # check if matches sdctf{
        d()
    if c[int(chr(45) + chr(49))] != chr(125): #check if ends with }
        d()
    g = c[6:-1].encode() # content inside {}
    if bytes( (g[i] ^ (a(i) & 0xff) for i in range(len(g))) ) != f:
        d()
    print('Nice job. You got the correct flag!')

def solve(f):
    flag = ''
    for i in range(len(f)):
        res = f[i] ^ (a(i) & 0xff)
        char = chr(res)
        flag += char
    print(flag)

if __name__ == "__main__":
    #e(b't2q}*\x7f&n[5V\xb42a\x7f3\xac\x87\xe6\xb4')
    solve(b't2q}*\x7f&n[5V\xb42a\x7f3\xac\x87\xe6\xb4')
else:
    eval(__import__("sys").exit(0))
```

**OUTPUT :**

```
v3ry-t4sty-sph4g3tt1
```