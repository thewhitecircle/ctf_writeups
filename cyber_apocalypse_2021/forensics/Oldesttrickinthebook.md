---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Oldest trick in the book Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: forensics
challenge: Oldest trick in the book
tags: "forensics, twh, pcap, tshark, python"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Oldest trick in the book

> Solved by : thewhiteh4t


* We are given a pcap which consists of mostly TLS and ICMP traffic
* ICMP looks promising as we can see the header of ZIP file `PK`

![](https://i.imgur.com/bcZ05AG.png)

* Another thing was that the the traffic from both IP address was similar I focused on only one of them


![](https://i.imgur.com/Iq12mh1.png)

* To extract data of all these packets I used tshark

```bash
$ tshark -r older_trick.pcap -T fields -e data.data -Y "ip.src == 192.168.1.7" > 192.168.1.7.txt
```

* After this I looked for duplicate packets in the text file

![](https://i.imgur.com/NoiYgXk.png)

* So we have 10127 unique icmp data packets
* To decode hex and compile all the data I created a small python script
* But I was not getting proper file format of resultant file so I inspected the data
* There were duplicates in the data as well!

```
b7ae04 0000000000 504b0304140000000000729e8d52659b 504b0304140000000000729e8d52659b 504b030414000000
ead104 0000000000 4c6b1800000018000000100000006669 4c6b1800000018000000100000006669 4c6b180000001800
99e804 0000000000 6e692f6164646f6e732e6a736f6e7b22 6e692f6164646f6e732e6a736f6e7b22 6e692f6164646f6e
cafb04 0000000000 736368656d61223a362c226164646f6e 736368656d61223a362c226164646f6e 736368656d61223a
```

* This is the data from first 4 packets for an example
* After first 6 characters we have 10 zeroes
* After that a unique string
* The string is repeated after that
* Then a partial repetition can be seen at the end
* I tried various combinations and in the end only the unique string was needed from each packet i.e `504b0304140000000000729e8d52659b` for first line as an example

```python
#!/usr/bin/env python3
import binascii
msg = []
with open('unique.txt', 'r') as raw:
    raw_arr = raw.readlines()
for line in raw_arr:
    if len(line) == 97:
        line = line.strip()
        line = line[16:48]
        plain = binascii.unhexlify(line)
        msg.append(plain)
with open('result.zip', 'wb') as res:
    for line in msg:
        res.write(line)
```

* The script iterates over each line in the file and skips empty lines if it finds any
* Then it slices of extra characters as stated above
* Then it decodes the hex into binary data and appends it in a file

![](https://i.imgur.com/H2fC7L3.png)

* And we get a proper zip file!
* Here are the extracted contents of the zip

![](https://i.imgur.com/8a1GAJB.png)

* After some enumeration of all files they point towards Mozilla Firefox
* After some googling I found that this is a firefox profile dump
* In linux the default path for profiles is `/home/user/.mozilla/firefox`
* I copied the folder into profiles folder and then edited the `profiles.ini` file present inside it to add the following entry

```
[Profile2]
Name=fini
Path=fini
IsRelative=1
```

* After this I launched firefox from CLI using 

```bash
$ firefox -P
```

* It provides an option to choose a specific profile and launch the browser with it

![](https://i.imgur.com/KZzqT9x.png)

* After the browser launched with the new profile and checked the saved logins and here we have the flag!

![](https://i.imgur.com/dHKQmSM.png)