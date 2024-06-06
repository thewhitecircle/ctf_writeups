---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Needle in a Haystack Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: re
challenge: Needle in a Haystack
tags: "re, legend"
date: 2023-03-27T00:00:00+00:00
last_update: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

##  Needle in a Haystack 
> Solved by Legend

Challenge description


> You've obtained an ancient alien Datasphere, containing categorized and sorted recordings of every word in the forgotten intergalactic common language. Hidden within it is the password to a tomb, but the sphere has been worn with age and the search function no longer works, only playing random recordings. You don't have time to search through every recording - can you crack it open and extract the answer?

In this challenge we are given a ELF 64-bit executable file.

![](https://i.imgur.com/cLU2TWx.png)

![](https://i.imgur.com/N3xkosj.png)


The challenge hint said that the password is `hidden within` so first thing I did was run the `strings` command to check if something is there and found the flag.

![](https://i.imgur.com/dbJZHa3.png)

