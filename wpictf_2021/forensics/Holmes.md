---
layout: load_md
title: The White Circle | Wpictf 2021 | Holmes Writeup
desc: Check out our writeup for Holmes for Wpictf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: forensics
challenge: Holmes
tags: "forensics, starry"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---



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