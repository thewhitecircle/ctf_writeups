---
layout: load_md
title: The White Circle | Wpictf 2021 | wpi admin 2 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: web
challenge: wpi admin 2
tags: "web, twh, sql, sqli"
---

<h1 class="heading card-title white-text">Wpictf 2021</h1>

## wpi admin 2

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

