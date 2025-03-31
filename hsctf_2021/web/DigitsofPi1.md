---
layout: load_md
title: The White Circle | Hsctf 2021 | Digits of Pi 1 Writeup
desc: Check out our writeup for Digits of Pi 1 for Hsctf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hsctf 2021
parent: hsctf_2021
category: web
challenge: Digits of Pi 1
tags: "web, starry"
date: 2021-06-20T00:00:00+00:00
last_modified_at: 2021-06-20T00:00:00+00:00
---


> Solved By : Starry-Lord

First thing, check the cells and find a formula. 

![](https://i.imgur.com/MT1NzjD.png)

Formula seems to be pointing to another sheet's called Source and its range 'A:B'. 

When you use spreadsheets, the accessibility tools actually help in un hiding hidden sheets, like the Source sheet here. 

To activate accessibility tools, go to Tools ('Outils' on the picture), and select Accessibility tools. 

![](https://i.imgur.com/2Rx5UPs.png)

Accessibility Tool include a way to call a range from the document so Monkey see🐒, Monkey do 🐒:

![](https://i.imgur.com/vlt5BiM.png)

**Note: it turns out you can reach a hidden sheet's full range by just typing its name ('Source' instead of 'Source'!A:B)**

It reveals the hidden sheet! On the bottom left you can see it got added to the panes. 

![](https://i.imgur.com/DJVGo4h.png)

And as suspected, you can find the flag in there somewhere. Thanks to my team mates for finding the flag for me

![](https://i.imgur.com/7ncbEkI.png)