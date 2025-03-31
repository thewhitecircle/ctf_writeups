---
layout: load_md
title: The White Circle | Cyber Apocalypse 2022 | WIDE Writeup
desc: Check out our writeup for WIDE for Cyber Apocalypse 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2022
parent: cyber_apocalypse_2022
category: re
challenge: WIDE
tags: "re, ava, ghidra"
date: 2022-05-21T00:00:00+00:00
last_modified_at: 2022-05-21T00:00:00+00:00
---


> Solved by - avantika(iamavu)

We load the binary in Ghidra, and take a look at functions, the menu function was interesting

![](https://i.imgur.com/oKi96Nx.png)

We look at the decompile part, and it shows a string, let’s try to enter this by running the binary

![](https://i.imgur.com/qrTmE1Q.png)

and we get our flag

![](https://i.imgur.com/3alOnG1.png)

FLAG - `HTB{str1ngs_4r3nt_4lw4ys_4sc11}`