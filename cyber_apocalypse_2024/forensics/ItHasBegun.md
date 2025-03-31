---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | It Has Begun Writeup
desc: Check out our writeup for It Has Begun for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: forensics
challenge: It Has Begun
tags: "forensics, starry"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by : Starry-Lord

We only get a script.sh for this challenge

![script.sh content](https://i.imgur.com/MyKDVK7.png)


Running this file will actually kill your current user session authentication. Upon closer examination of what it does we can see that the host name for the ssh key looks awfully suspicious, and that a base64 string is being executed in bash at the end.

Reverse 1 and decode 2 for the flag:

```
HTB{w1ll_y0u_St4nd_y0uR_Gr0uNd!!}
```

