---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Butter Overflow Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: pwn
challenge: Butter Overflow
tags: "pwn, taz, bof"
---

<h1 class="heading card-title white-text">Hacktivitycon 2021</h1>

## Butter Overflow
> Solved by: Taz34


- As the name suggested it’s a buffer overflow challenge 
- So I started by giving huge inputs 
- And further narrowed it down and found the offset
- The offset is 520 so we need 521 characters to do a buffer overflow to read the flag
![](https://i.imgur.com/iiEQowN.png)


