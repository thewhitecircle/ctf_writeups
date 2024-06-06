---
layout: load_md
title: The White Circle | Wpictf 2021 | Holmes Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: forensics
challenge: Holmes
tags: "forensics, starry"
date: 2021-04-26T00:00:00+00:00
last_update: 2021-04-26T00:00:00+00:00
---

<h1 class="heading card-title white-text">Wpictf 2021</h1>

## Holmes

> Solved by : Starry-Lord

* the challenge gives us the following string 

```
Z29vZCB0aG91Z2h0LCBidXQgbm8u
```

* it looks base64 , however  base64 decoder shows the following:

```
good thought, but no.
```

* using `Sherlock` OSINT tool we found this github repository :

```
https://github.com/Z29vZCB0aG91Z2h0LCBidXQgbm8u/flag/blob/main/README.md
```

```
WPI{sh3rlock_holmes_w0uld_be_pr0ud}
```