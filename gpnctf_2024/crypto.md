# crypto

## never gonna let you crypto
> Solved by : thewhiteh4t


- In this challenge we are given a python script and cipher text in hex

```python
import os
def encrypt(message,key):
    message = message.encode()
    out = []
    for i in range(len(message)):
        out+= [message[i]^key[i%len(key)]]
    return bytes(out).hex()
FLAG = "GPNCTF{fake_flag}"
key = os.urandom(5)

print(encrypt(FLAG,key))
```

- each byte of the message was XORed against a byte of the key
- key length was 5 and key was random
- we can assume known plain text message as `GPNCTF` i.e. of length 6 which is longer than the key
- we can perform known plain text attack : 

```python
#!/usr/bin/env python3

known_plaintext = "GPNCTF"

ciphertext = "d24fe00395d364e12ea4ca4b9f2da4ca6f9a24b2ca729a399efb2cd873b3ca7d9d1fb3a66a9b73a5b43e8f3d"

ciphertext_bytes = bytes.fromhex(ciphertext)

key = b""

for i in range(len(known_plaintext)):
    key_byte = ciphertext_bytes[i] ^ known_plaintext.encode()[i]
    key += bytes([key_byte])

print(key)

key = key[:5]
dec_msg_bytes = b""

for i in range(len(ciphertext_bytes)):
    decrypted_byte = ciphertext_bytes[i] ^ key[i % len(key)]
    dec_msg_bytes += bytes([decrypted_byte])

decrypted_message = dec_msg_bytes.decode()
print(decrypted_message)
```

```
$ python decode.py
b'\x95\x1f\xae@\xc1\x95'
GPNCTF{One_T1me_p4ds_m4y_n3v3r_b3_r3u53d!!!}
```
