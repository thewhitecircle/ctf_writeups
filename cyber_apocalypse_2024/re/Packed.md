---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Packed Writeup
desc: Check out our writeup for Packed for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: re
challenge: Packed
tags: "re, ava, upx"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> solved by : avantika(@iamavu)

we are given a binary, called packed i.e this has been obfuscated/packed by the dev, which hinders our exploitation and reversing as it’s very hard to understand what’s going on

we use checksec command provided by pwntools to check which packer they have used
`pwn checksec packed`


![](https://i.imgur.com/v8mMY4p.png)


as we can observe it has been packed with UPX, quite the popular packer, now we just need to unpack it to see all the functions properly, so we can dissect it using the following command
`upx -d -o unpacked packed`
we would now have binary called unpacked in our current directory, which will be unpacked and not obfuscated
fire up your ghidra, let’s have a look at the functions, the `entered` function has our flag in plaintext, so pretty much easy win
`HTB{unp4ck3d_th3_s3cr3t_0f_th3_p455w0rd}`
pwned!

