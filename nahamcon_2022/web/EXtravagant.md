---
layout: load_md
title: The White Circle | Nahamcon 2022 | EXtravagant Writeup
desc: Check out our writeup for EXtravagant for Nahamcon 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: web
challenge: EXtravagant
tags: "web, nigamelastic, xxe"
date: 2022-05-03T00:00:00+00:00
last_modified_at: 2022-05-03T00:00:00+00:00
---



> Solved By : nigamelastic
![](https://i.imgur.com/rmw9shq.png)

The challenge mentions the following:

```
The flag is in /var/www
```

on accessing the website we see a normal interface with xml parsing as a service

![](https://i.imgur.com/iWDrdSZ.png)

from the mentioning of XML it seems that this might be an XXE

Since we already know the location of the flag I used the following payload:

![](https://i.imgur.com/sqelqWg.png)

I simply uploaded it to the trial tab:

![](https://i.imgur.com/h9WG0EH.png)

![](https://i.imgur.com/90bhiq3.png)

and then used view XML tab to view my xml

![](https://i.imgur.com/z5PUs40.png)

This would give flag

![](https://i.imgur.com/Tp2Wy2s.png)

