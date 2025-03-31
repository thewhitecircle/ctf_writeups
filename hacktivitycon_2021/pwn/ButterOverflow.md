---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Butter Overflow Writeup
desc: Check out our writeup for Butter Overflow for Hacktivitycon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: pwn
challenge: Butter Overflow
tags: "pwn, taz, bof"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---


> Solved by: Taz34


- As the name suggested it’s a buffer overflow challenge 
- So I started by giving huge inputs 
- And further narrowed it down and found the offset
- The offset is 520 so we need 521 characters to do a buffer overflow to read the flag
![](https://i.imgur.com/iiEQowN.png)


