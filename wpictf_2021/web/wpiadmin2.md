---
layout: load_md
title: The White Circle | Wpictf 2021 | wpi admin 2 Writeup
desc: Check out our writeup for wpi admin 2 for Wpictf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: web
challenge: wpi admin 2
tags: "web, twh, sql, sqli"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---



> Solved by : thewhiteh4t

* we login as dennisb@uupeye.edu again
* In student communication we get the link to admin portal : https://wpiadmin.wpictf.xyz/iyghfihGBKHJF9719fn113
* Bruteforcing did not work so I tried SQL login bypass using burpsuite

```
    admin@uupeye.edu' or '1'='1
```

![](https://i.imgur.com/ah9fjqm.png)

* Bypass is successful and we get the link for admin portal!

![](https://i.imgur.com/sUzWapJ.png)

