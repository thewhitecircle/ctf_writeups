---
layout: load_md
title: The White Circle | Nahamcon 2024 | Hashes on Hashes on Hashes Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: scripting
challenge: Hashes on Hashes on Hashes
tags: "scripting, twh, encryption, xor, python"
date: 2024-05-28T00:00:00+00:00
last_update: 2024-05-28T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>


## Hashes on Hashes on Hashes
> Solved by : thewhiteh4t


- We have a python script `server.py` and decryption log
- log contains a fixed pattern : 

```
20/05/2024 15:14:38        Listening for connection...
20/05/2024 15:14:42        Connection received from ('10.10.2.6', 50924)
20/05/2024 15:14:42        Received encrypted message GyseOBdHRQ49Mz1h
20/05/2024 15:14:42        Key loaded for encrypted message
20/05/2024 15:14:42        Key expanded by factor of 1
20/05/2024 15:14:42        Partial message digest is c1d9f50f86825a1a2302ec2449c17196
20/05/2024 15:14:42        Partial message digest is a64cf5823262686e1a28b2245be34ce0
20/05/2024 15:14:42        Partial message digest is 6b6e667a40e816c4da7bb4ab64cbb82b
20/05/2024 15:14:42        Partial message digest is 1824e8e0307cbfdd1993511ab040075c
20/05/2024 15:14:42        Partial message digest is 8b1a9953c4611296a827abf8c47804d7
20/05/2024 15:14:42        Partial message digest is d1a7fb5eab1c16cb4f7cf341cf188c3d
20/05/2024 15:14:42        Partial message digest is d245114cd44bdcd540a014ad78257432
20/05/2024 15:14:42        Partial message digest is fd05d5cc96e2e85fa2dfc2505932cf76
20/05/2024 15:14:42        Partial message digest is cd3719c301dce67f4440f42be83ef6f3
20/05/2024 15:14:42        Partial message digest is ad7fe93595f81645e06239701fbd8084
20/05/2024 15:14:42        Partial message digest is e8ea7a8d1e93e8764a84a0f3df4644de
20/05/2024 15:14:42        Partial message digest is 9d6a2963872077db674a27a39c492e61
20/05/2024 15:14:42        Message fully decrypted, ready to send...
20/05/2024 15:14:42        Decrypted message sent!
```

- server.py contains the `decrypt` function : 

```python
def decrypt(encrypted):
    key = open('key.txt').read()
    key = key.strip()
    log.print("Key loaded for encrypted message")

    factor = len(encrypted) // len(key) + 1
    key = key * factor
    log.print(f"Key expanded by factor of {factor}")
    key_bytes = key.encode()

    enc_bytes = base64.b64decode(encrypted)
    dec_bytes = bytearray()

    for i in range(len(enc_bytes)):
        dec_bytes.append(enc_bytes[i] ^ key_bytes[i])
        log.print(f"Partial message digest is {md5(dec_bytes).hexdigest()}")
    decrypted = dec_bytes.decode()
    log.print("Message fully decrypted, ready to send...")
    return decrypted
```

- key is read from a file which we do not have
- key is repeated by factor and the factor value is available in the log
- key is encoded into bytes
- encrypted message is base64 decoded
- each byte of the encrypted message is XORed against one byte of the key based on the index of the encrypted message
- the decoded byte is appended to a byte array
- md5sum of the bytearray is calculated which is also available in the log
- now what we have : 

```
1. encrypted message
2. key factor
3. md5 digest
```

- reverse of XOR is XOR, we can bruteforce they single key byte used by calculating md5sum for each combination and comparing it with the md5 present in the log
- if the md5 matches then we have one correct byte of the key, we can extract full key and decode the messages
- script : 

```python
#!/usr/bin/env python3

import base64
import hashlib

with open('decryption_server.log', 'r') as infile:
        logs = infile.read()


def guess_key_byte(encrypted, digests):
        enc_bytes = base64.b64decode(encrypted)
        dec_bytes = bytearray()
        key_bytes = bytes()

        for i in range(len(enc_bytes)):
                for num in range(256):
                        key_int = enc_bytes[i] ^ num

                        dec_bytes.append(key_int)

                        md5_digest = hashlib.md5(dec_bytes).hexdigest()

                        if md5_digest == digests[i]:
                                key_bytes += bytes([num])
                                break
                        else:
                                dec_bytes = dec_bytes[:-1]
        return key_bytes


chunks = logs.split('Listening for connection...')

for chunk in chunks:
        lines = chunk.split('\n')

        if len(lines) < 4:
                continue

        dec_bytes = bytearray()
        digests = []

        for line in lines:
                if 'Received encrypted message' in line:
                        enc_msg = line.split(' ')[-1]
                if 'Key expanded' in line:
                        key_factor = int(line.split(' ')[-1])
                if 'Partial message' in line:
                        msg_digest = line.split(' ')[-1]
                        digests.append(msg_digest)

        partial_key_bytes = guess_key_byte(enc_msg, digests)
        # print(partial_key_bytes)

        dec_key = partial_key_bytes * key_factor

        dec_key_bytes = dec_key
        enc_bytes = base64.b64decode(enc_msg)
        dec_msg_bytes = bytearray()
        for i in range(len(enc_bytes)):
                dec_msg_bytes.append(enc_bytes[i] ^ dec_key_bytes[i])
        decrypted = dec_msg_bytes.decode()
        print(decrypted)
```


![](https://i.imgur.com/GRpwnLU.png)



