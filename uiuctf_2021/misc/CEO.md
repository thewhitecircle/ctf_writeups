---
layout: load_md
title: The White Circle | Uiuctf 2021 | CEO Writeup
desc: Check out our writeup for CEO for Uiuctf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Uiuctf 2021
parent: uiuctf_2021
category: misc
challenge: CEO
tags: "misc, twh, aircrack"
date: 2021-08-12T00:00:00+00:00
last_modified_at: 2021-08-12T00:00:00+00:00
---


> Solved by : thewhiteh4t

- We have a `.cap` file in this challenge
- its a handshake file which can be used with `aircrack-ng`


    aircrack-ng megacorp-01.cap -w /usr/share/wordlists/rockyou.txt


![](https://i.imgur.com/fkJw0Nl.png)



    uiuctf{nanotechnology}


