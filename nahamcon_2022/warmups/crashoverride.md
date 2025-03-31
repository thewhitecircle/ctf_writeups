---
layout: load_md
title: The White Circle | Nahamcon 2022 | crash override Writeup
desc: Check out our writeup for crash override for Nahamcon 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: warmups
challenge: crash override
tags: "warmup, twh"
date: 2022-05-03T00:00:00+00:00
last_modified_at: 2022-05-03T00:00:00+00:00
---


> solved by : thewhiteh4t

Basic buffer overflow challenge. In the c code we can see that buffer size is 2048, I just sent 2060 “A” and got the flag

![](https://i.imgur.com/PbQlZfr.png)

