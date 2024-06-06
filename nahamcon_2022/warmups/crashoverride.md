---
layout: load_md
title: The White Circle | Nahamcon 2022 | crash override Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: warmups
challenge: crash override
tags: "warmup, twh"
date: 2022-05-03T00:00:00+00:00
last_modified_at: 2022-05-03T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2022</h1>

## crash override
> solved by : thewhiteh4t

Basic buffer overflow challenge. In the c code we can see that buffer size is 2048, I just sent 2060 “A” and got the flag

![](https://i.imgur.com/PbQlZfr.png)

