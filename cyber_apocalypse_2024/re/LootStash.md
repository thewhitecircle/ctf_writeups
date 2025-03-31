---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | LootStash Writeup
desc: Check out our writeup for LootStash for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: re
challenge: LootStash
tags: "re, starry, ghidra"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by : Starry-lord

Since I don’t really dive into reversing usually, I decided to try my luck with tools like ghidra for the occasion.

![recon on the file](https://i.imgur.com/3zMCcVZ.png)


We can see the binary seems to output a random string, searched into a list then closes the program.

Immediately curious to see what was the list, I looked for it into ghidra and eventually cycling through the functions, you can find it.


![sentence list found](https://i.imgur.com/w3didgB.png)


Then curiosity made the rest possible, since i wanted to find some sort of pattern or something that would stand out:

![finding the flag v1](https://i.imgur.com/hNrPN1w.png)


After all of this, realised you could’ve actually just used strings:


![](https://i.imgur.com/FxDZCAC.png)


```
HTB{n33dl3_1n_a_l00t_stack}
```

