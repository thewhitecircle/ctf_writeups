---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Questionnaire Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: pwn
challenge: Questionnaire
tags: "pwn, legend"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Questionnaire
> Solved by Legend

Challenge description


> It's time to learn some things about binaries and basic c. Connect to a remote server and answer some questions to get the flag.

In this challenge we are given a binary along with it’s source code `test.c`

![](https://i.imgur.com/7tZP4A4.png)


Executing the binary asks for a payload which we don’t have so I looked into the C code that was provided.

![](https://i.imgur.com/0JpTvav.png)


Going through the C code we can see that the flag is in the `gg()` function but it’s never called from either `main()` or `vuln()` function so we can not get the flag from this. And there is also the comment stating that we need to connect to the challenge with netcat.

![](https://i.imgur.com/xlXXRBD.png)


Once connected it shows that there will be simple questionnaire to get the flag along with theory of information required to solve a simple binary challenge.

![](https://i.imgur.com/SKfQ944.png)


The 0x1 question is to check the bit for the binary . We already ran `file` on the binary and got the answer that is `64-bit`.

![](https://i.imgur.com/msgy5CB.png)


In 0x2 question is to check the linking of the binary. Again using the `file` command we can get the answer that is `dynamic`.

![](https://i.imgur.com/X6sVhXJ.png)


In 0x3 it’s asking it’s stripped or not stripped binary. Again using `file` and the answer is `not stripped`.

![](https://i.imgur.com/VtbpJvr.png)


In 0x4 they are asking the protections enabled in the binary. Using `checksec` we can get the answer `NX`.

![](https://i.imgur.com/vFjxVKG.png)

![](https://i.imgur.com/rfxL9Ut.png)


In 0x5 the are asking the custom function getting called in `main()`. We saw from the code that it’s `vuln()`.

![](https://i.imgur.com/IhVR8hh.png)


In 0x6 it’s asking the size of the buffer. Answer is `0x20` which is written in the C code.

![](https://i.imgur.com/wRh19aZ.png)


In 0x7 it’s asking the function which never get’s called. The answer is `gg()` as we saw initially.

![](https://i.imgur.com/JT74yo3.png)


In 0x8 it’s asking the name of function which could trigger Buffer Overflow. Answer is `fgets()` written in the C code.

![](https://i.imgur.com/68XULCQ.png)


In 0x9 it’s asking after how many input of bytes the Segmentation fault will occur. Answer is `40` we can manually test it by giving the input to the binary.

![](https://i.imgur.com/b4x8Q81.png)

![](https://i.imgur.com/6NKJU12.png)


In 0xa question they are asking the address of the function `gg`. We can get this using `p gg` and the answer is `0x401176`.
Once we enter the final answer it will print out the flag.

![](https://i.imgur.com/IcWjz9r.png)

![](https://i.imgur.com/d6MBDeV.png)

