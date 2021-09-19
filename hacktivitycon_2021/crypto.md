# crypto

## Hexahedron
> Solved by: Taz34


- We are give values of n,e and c.
- Decode them into simple numeric form using python.
![](https://i.imgur.com/z1yvvX8.png)

- it looks like RSA, we used RsaCtfTool to decipher it.

: https://github.com/Ganapati/RsaCtfTool


    python3 RsaCtfTool.py -n 112339816301925396926211289689793745814213925314273886071305785874178028552510482239036537066616690493241410015435402110525284201411608164205573122430898583517515498250410244592963132324072861567753086739636553410154316180827724708002409356129254383468446158145079982391991062389788544378839486986385137994309 -e 3 --uncipher 2217344750798178599616518881851238192046537371134831984828894413752520937378161486880269974456574131502921272953104454680926482208357166098075344508240480152890914678813031666242202555794691235412837030045499161787224264164243336308650477343133919653356349913604131486721125


![](https://i.imgur.com/vOzC4jK.png)


here we have the flag.

---

## N1TP
> Solved By : choco

We are given an encrypted flag with randomized key.
The hint is that this uses a one time pad encryption

![](https://i.imgur.com/vxsQdmu.png)


The general gist of one pad encryption:
You have a plain text: P = “HELLO” (8 5 12 12 15)
We are given a key: K = “DREFX” (4 18 5 6 24)

The encryption comes in when we add both P and K to get encryption C = “L W Q R M” (12 23 17 18 13(39%26))

So P + K = (C%N) where N is the letter range
To get the decipher text we simple subtract C with K
P = “HELLO” (8 5 12 12 15(11%26)

So (P%N) = C - K

The vulnerability comes in if the same key K is used to encrypt another plain text provided we know the range of text used along with the length

Suppose P1 = “AAAAA” (1 1 1 1 1)
P1 + K = C1
C1 = “ETFGY” (5 19 6 7 25)

We could easily get the key if it is within the modulus range
C1 - P1 = K
K = “DREFX” (4 18 5 6 24)

We can now use this to get P with the given C

But another way to find P is using simple logic

C1 - P1 = C - P
So P = C - C1 + P1
All we need to do is 
find C - C1 to 0 and we can guess P 

Using this logic we will find the flag 
The code for difference C - C1 is given below and difference is added to P1
and manually repeated until we get C - C1 as 0


    import binascii
    
    A = "dc0de91facbe90ee6f652167906ee0d17123cf9e746a63db4b4e7d93040f59331ead9be0b2fe"
    Ahex = binascii.unhexlify(A) 
    B = "dc0de91facbe90ee6f652167906ee0d17123cf9e746a63db4b4e7d93040f59331ead9be0b2fe"
    
    Al = list(bytearray(Ahex))
    Bl = list(bytearray(binascii.unhexlify(B)))
    C = "flag{00000000000000000000000000000000}"
    
    Chex = list(bytearray(C))
    print(Chex)
    
    d = []
    for i in range(len(Al)):
        d.append(Al[i] - Bl[i])
    print(d)
    
    k = []
    c = []
    for i in range(len(Al)):
        k.append(Chex[i] + d[i])
        if((48 <= k[i] <= 57) or (97 <= k[i] <= 102) or k[i] == 108 or k[i]==103 or k[i]==123 or k[i]==125):
                c.append(chr(k[i]))
        else:
                c.append("#")
    print(k)
    print(c)


![](https://i.imgur.com/LIE6yxg.png)


flag: `flag{9276cdb76a3dd6b1f523209cd9c0a11b}`

---

## TRIFORCE
> Solved By : choco

This is a CBC AES encryption (Chained Block Cipher)

Suppose the plain text P is 64 bits long
It is then split into 4 parts (16 bit each)

There is an initializer iv that is 16 bits long and key k (16 bits long)
The cipher text for first part of P is

C0 = E(P0 ^ iv,k)
and following chain goes like this
Ci = E(Pi ^ Ci-1,k) i > 0 


![Cipher block chaining (CBC) mode encryption](https://i.imgur.com/5PtFyrs.png)


The decryption does like this

![Cipher block chaining (CBC) mode decryption](https://i.imgur.com/3KnZgU3.png)


Where P0 = D(C0,k) ^ iv
and following decryption goes
Pi = D(Ci,k) ^ Ci-1

If the key k and vi are same, a vulnerability arises
During decryption,
P0 = D(C0,k) ^ k
P1 = D(C1,k) ^ C0
P2 = D(C2,k) ^ C1

if we replace C2 with C0
P2 = D(C0,k) ^ C1

xor the result with any of the two inputs will give the other input
D(C0,k) = P2 ^ C1
P0 = D(C0,k) ^ k
k = D(C0,k) ^ P0
therefore, k = P2 ^ C1 ^ P0
so since we know P0,P2 and C1, we can easily find k

Hence all we need to do is to replace the 3rd block of ciphertext with the first block of cipher text,
Xor the result of 3rd plain text block with 2nd cipher text block and xor that result with 1st plaintext block to get the key 

Do this three times with the different keys above to get the flag
input: `616161616161616161616161616161616161616161616161616161616161616161616161616161616161616161616161`

Code for replacing ciphertext:


    def listToString(s): 
        str1 = ""   
        for ele in s: 
            str1 += ele   
        return str1 
    
    def chunk(in_string,num_chunks):
        chunk_size = len(in_string)//num_chunks
        if len(in_string) % num_chunks: chunk_size += 1
        iterator = iter(in_string)
        for _ in range(num_chunks):
            accumulator = list()
            for _ in range(chunk_size):
                try: accumulator.append(next(iterator))
                except StopIteration: break
            yield ''.join(accumulator)
            
    string = "e5cc7f05f279ca152d3fbd32d899b55fec8de5d9ae3d62bac51195ddedc0863462021d3c51a4a56555ca95ff4303b764a02a2f44327cb2007d46de117900720a"
    l = list(chunk(string,4))
    l[2] = l[0]
    print(l)
    out = listToString(l)
    print(out)

code for getting hex for part of flag:

    def chunk(in_string,num_chunks):
        chunk_size = len(in_string)//num_chunks
        if len(in_string) % num_chunks: chunk_size += 1
        iterator = iter(in_string)
        for _ in range(num_chunks):
            accumulator = list()
            for _ in range(chunk_size):
                try: accumulator.append(next(iterator))
                except StopIteration: break
            yield ''.join(accumulator)
    
    string = "6227616161616161616161616161616161616161616161616161616161616161e8c6e5dfb46432e2c2499084e899d462e6af4534aed0627d75f825c096970f36"
    l = list(chunk(string,4))
    c = "ec8de5d9ae3d62bac51195ddedc08634"
    
    print(hex(int(l[2],16)))
    
    d = int(l[2],16) ^ int(c,16)
    print(hex(d))
    e = d ^ int(l[0],16)
    print(hex(e))
![](https://i.imgur.com/TgyoAnU.jpg)


 
do this and replace each cipher for each key 
each keys in hex = `666c61677b3831396639643864383337` `32316163346334343262313635396633` `36646632647d20202020202020202020`

flag: `**flag{819f9d8d83721ac4c442b1659f36df2d}**`

