---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Flag Command Writeup
desc: Check out our writeup for Flag Command for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: web
challenge: Flag Command
tags: "web, legend, api"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by: Legend

Challenge description:

```
Embark on the "Dimensional Escape Quest" where you wake up in a mysterious forest maze that's not quite of this world. Navigate singing squirrels, mischievous nymphs, and grumpy wizards in a whimsical labyrinth that may lead to otherworldly surprises. Will you conquer the enchanted maze or find yourself lost in a different dimension of magical challenges? The journey unfolds in this mystical escape!
```

In this challenge the website is a game. After interacting with for a few minutes saw that it is running with `API` mostly. 
In burp I saw there was options which showed all the game command which can be used.


![](https://i.imgur.com/e13FyAu.png)


In that there was a `secret` command which seemed interesting.  Just gave that command as input and got the flag.

![](https://i.imgur.com/f3UdedV.png)

