---
layout: load_md
title: The White Circle | Uiuctf 2021 | CEO Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Uiuctf 2021
parent: uiuctf_2021
category: misc
challenge: CEO
tags: "misc, twh, aircrack"
---

<h1 class="heading card-title white-text">Uiuctf 2021</h1>

## CEO
> Solved by : thewhiteh4t

- We have a `.cap` file in this challenge
- its a handshake file which can be used with `aircrack-ng`


    aircrack-ng megacorp-01.cap -w /usr/share/wordlists/rockyou.txt


![](https://i.imgur.com/fkJw0Nl.png)



    uiuctf{nanotechnology}


