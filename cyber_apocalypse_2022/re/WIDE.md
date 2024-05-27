---
layout: load_md
title: The White Circle | Cyber Apocalypse 2022 | WIDE Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2022
parent: cyber_apocalypse_2022
category: re
challenge: WIDE
tags: "re, ava, ghidra"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2022</h1>

## WIDE
> Solved by - avantika(iamavu)

We load the binary in Ghidra, and take a look at functions, the menu function was interesting

![](https://i.imgur.com/oKi96Nx.png)

We look at the decompile part, and it shows a string, let’s try to enter this by running the binary

![](https://i.imgur.com/qrTmE1Q.png)

and we get our flag

![](https://i.imgur.com/3alOnG1.png)

FLAG - `HTB{str1ngs_4r3nt_4lw4ys_4sc11}`