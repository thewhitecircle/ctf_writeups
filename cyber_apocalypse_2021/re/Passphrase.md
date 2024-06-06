---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Passphrase Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: re
challenge: Passphrase
tags: "re, chronocruz, ida"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Passphrase

> Solved by chronocruz.exe

Disassembling the binary in IDA we reach the first code block where a certain portion of the code caught my eye

![](https://i.imgur.com/rCnB49a.png)

* So I wrote down the string given here

```
3xtr4t3rR3stR14L5_VS_hum4n5
```

* Tried using this string in the program and voila!

![](https://i.imgur.com/04dcCGF.png)