---
layout: load_md
title: The White Circle | Nahamcon 2022 | Steam Locomotive Writeup
desc: Check out our writeup for Steam Locomotive for Nahamcon 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: misc
challenge: Steam Locomotive
tags: "misc, legend"
date: 2022-05-03T00:00:00+00:00
last_modified_at: 2022-05-03T00:00:00+00:00
---


> Solved by: Legend

In this challenge we were provided with a ssh credentials to get the flag, with a hint that ls command was getting mistyped accidentally.

When I connect to the ssh it was playing an animation of steam engine, `sl` command which is Steam Locomotive, and then when the animation was over then immediately the session was getting disconnected.

![](https://i.imgur.com/zW5ZZwB.png)

To retrieve the flag we needed to read the flag and since ssh allows direct command execution during connection we can read the flag using that.

![](https://i.imgur.com/14tSoKE.png)