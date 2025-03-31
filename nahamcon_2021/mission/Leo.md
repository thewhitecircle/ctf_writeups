---
layout: load_md
title: The White Circle | Nahamcon 2021 | Leo Writeup
desc: Check out our writeup for Leo for Nahamcon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: mission
challenge: Leo
tags: "web, git"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---



Directory search on https://constellations.page reveals `/.git/` directory

we dont have access to this repository because its not public on github so we can use a nice tool to dump directly from the website!

https://github.com/internetwache/GitTools

then if we check `git log` we can see full name of leo

![](https://i.imgur.com/R8hJZXS.png)

Instagram : `@_leorison`

there is a QR Code in one of the images 

```
flag{636db5f4f0e36908a4f1a4edc5b0676e} 
    
A password for Leo is constelleorising
```

we found flag and another creds!

