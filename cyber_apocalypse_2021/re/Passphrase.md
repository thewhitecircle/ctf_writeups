---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Passphrase Writeup
desc: Check out our writeup for Passphrase for Cyber Apocalypse 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: re
challenge: Passphrase
tags: "re, chronocruz, ida"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---



> Solved by chronocruz.exe

Disassembling the binary in IDA we reach the first code block where a certain portion of the code caught my eye

![](https://i.imgur.com/rCnB49a.png)

* So I wrote down the string given here

```
3xtr4t3rR3stR14L5_VS_hum4n5
```

* Tried using this string in the program and voila!

![](https://i.imgur.com/04dcCGF.png)