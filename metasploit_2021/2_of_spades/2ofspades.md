---
layout: load_md
title: The White Circle | Metasploit 2021 | 2 of spades Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: 2_of_spades
challenge: 2 of spades
tags: "web, taz, nikto"
date: 2021-12-08T00:00:00+00:00
last_update: 2021-12-08T00:00:00+00:00
---

<h1 class="heading card-title white-text">Metasploit 2021</h1>
## 2 of spades

> Solved by: Taz34

Did a NIKTO scan on the target

```
nikto -h http://172.17.15.117:443/
```

and one of the lines in the result showed this :

```
+ /.env: .env file found. The .env file may contain credentials.
```

so i headed to this sub directory

![](https://i.imgur.com/OpsJm7G.png)

inserted the file name in the URL

```
http://172.17.15.117:443/3e6f0e21-7faa-429f-8a1d-3f715a520da4.png
```

![](https://i.imgur.com/BvCCwrW.png)

and we have the flag.