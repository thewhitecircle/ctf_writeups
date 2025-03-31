---
layout: load_md
title: The White Circle | Metasploit 2021 | 2 of spades Writeup
desc: Check out our writeup for 2 of spades for Metasploit 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: 2_of_spades
challenge: 2 of spades
tags: "web, taz, nikto"
date: 2021-12-08T00:00:00+00:00
last_modified_at: 2021-12-08T00:00:00+00:00
---


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