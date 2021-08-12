# crypto

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

---

## back_to_basics
> Solved by : choco

“Shoutout to those people who think that base64 is proper encryption”

We are given an encoded flag that contains a lot of letters 

The way encoding in this program works is the base depends on the key:

    ALPHABET = bytearray(b"0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ#")
    def base_n_encode(bytes_in, base):
        return mpz(bytes_to_long(bytes_in)).digits(base).upper().encode()
    def encrypt(bytes_in, key):
        out = bytes_in
        for i in key:
                print(i)
                out = base_n_encode(out, ALPHABET.index(i))
        return out

so if the key is for example = “KEY”
The string will be encoded three times
and for the first encoding, K is in position 21 in ALPHABET so string is encoded in base 21 (only contains chars 0-9 and A-J)
this encoded string is again encoded in base 15 (only contains chars 0-9 and A-D) since E is in 15th position in ALPHABET
then finally gets encoded in base 35 (only contains chars 0-9 and A-X) for Y

The decryption will behave similar but the key will be reverse “YEK”

Now, we can see a pattern here that for a given encoded string, **we will see that if we find the highest alphabet or number then the base would be a number greater than the position of that highest letter**

**example, if the highest letter for an encoded string is T then the key must be U,V,W,X,Y,Z or # i.e., base 30 to 36**

Great, now we have narrowed down the options to find the base for the first encoded data.

**To confirm if this decoded string is correct, we need to check if this string can be converted to ascii, if any string contains invalid ascii values then that base can be eliminated.**

most likely, if the output of string shows a proper ascii converted output then that is the base, else it will throw an error.


        C.decode()
    UnicodeDecodeError: 'utf-8' codec can't decode byte 0xfd in position 0: invalid start byte

So we can use an try except case for errors like to to confirm the base

Once, we find the base, we can save the decoded string to a file then repeat the process using this as the encoded string and so on till we get an error due to the program finding a { on the string

That’s where we know we got the flag!


    from Crypto.Util.number import long_to_bytes, bytes_to_long
    from gmpy2 import mpz, to_binary
    
    ALPHABET = bytearray(b"0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ#")
    
    def base_n_decode(bytes_in, base):
            print(base)
            bytes_out = to_binary(mpz(bytes_in, base=base))[:1:-1]
            return bytes_out
    
    
    def largest_alphabet(a, n) :
         
        # Initializing max alphabet to 'a'
        max = '0'
     
        # Find largest alphabet
        for i in range(n) :
            if (a[i] > max):
                max = a[i]
     
        # Returning largest element
        return max
    
    if __name__ == '__main__' :
        f = open("flag_enc", "r")
        j = 0
        while j < 36: 
            a = f.read()
            size = len(a)
            A = str.encode(largest_alphabet(a, size))
            f.close()
            j = 1
            while (ALPHABET.index(A)+j) < 37:
                   C = base_n_decode(a,ALPHABET.index(A)+j)
                   C.decode()
                   break;
                   j= j+1;
            if j > 36:
               break;
            print(chr(ALPHABET[ALPHABET.index(A)+j])) #print founded key
            f = open("flag", "wb")
            f.write(C)
            f.close()
            f = open("flag", "r")

**key for decryption is : WM5Z8CRJABXJDJ5W**

```
flag: uiuctf{r4DixAL}
```