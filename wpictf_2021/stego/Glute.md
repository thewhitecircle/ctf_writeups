---
layout: load_md
title: The White Circle | Wpictf 2021 | Glute Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: stego
challenge: Glute
tags: "stegano, ava, foremost"
date: 2021-04-26T00:00:00+00:00
last_update: 2021-04-26T00:00:00+00:00
---

<h1 class="heading card-title white-text">Wpictf 2021</h1>

## Glute

> Solved by : Ava

* we have a PNG image

![](https://i.imgur.com/7AUOHqb.png)

* running `foremost` reveals a JPG and PDF files inside it

```bash
    Foremost version 1.5.7 by Jesse Kornblum, Kris Kendall, and Nick Mikus
    Audit File
    
    Foremost started at Sat Apr 24 21:28:46 2021
    Invocation: foremost glute.png
    Output directory: /home/twh/ctf/wpictf/glute/output
    Configuration file: /etc/foremost.conf
    ------------------------------------------------------------------
    File: glute.png
    Start: Sat Apr 24 21:28:46 2021
    Length: 335 KB (343136 bytes)
    
    Num         Name (bs=512)               Size         File Offset         Comment
    
    0:        00000564.jpg               19 KB              288793         
    1:        00000000.png              335 KB                   0           (699 x 699)
    2:        00000557.pdf               55 KB              285674         
    Finish: Sat Apr 24 21:28:46 2021
    
    3 FILES EXTRACTED
            
    jpg:= 1
    png:= 1
    pdf:= 1
    ------------------------------------------------------------------
```

* PDF contains our flag

![](https://i.imgur.com/dgnNrXG.png)

```
    WPI{P0lyGlOtz_R_koo1}
```