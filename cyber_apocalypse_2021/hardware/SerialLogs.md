---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Serial Logs Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: hardware
challenge: Serial Logs
tags: "hardware, nigamelastic, salea, serial"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Serial Logs

> Solved by : Nigamelastic

* `.sal` files are saleae logic analyzer files. 
* I got this as reference https://dystopia.sg/allesctf20-PWNkemon/
so i downloaded the sla logic analyzer
https://www.saleae.com/downloads/
* from here after installing, i opened the sal files which i could and then the following:
* I imported the value i am pretty sure if i find the right analyzer it should work

![](https://i.imgur.com/mO5s5Lx.png)

* by far for this challenge async serial works the best
* challenge states raspberry pi which has a baud rate of 115200
* after some pondering i realized that there are some errors in parity on fixing them and using the `115200` in async serial we get the hex converting which gives us this:

https://www.dropbox.com/s/ztqoa16wvp6rvf6/message.txt?dl=0

![](https://i.imgur.com/1wJv0Ws.png)

* found this : https://6e726d.github.io/Challenge/Ekoparty15/
* this writeup explains how to get the baud rate
* open the SAL file and zoom

![](https://i.imgur.com/hgD3Jz4.png)

* zoom 

![](https://i.imgur.com/fzE0pOJ.png)

* zoom

![](https://i.imgur.com/qwj6blJ.png)

* zoom 

![](https://i.imgur.com/1Gl1HTX.png)

* until you see the smallest unit of the digital wave then measure it: as its 13.498

![](https://i.imgur.com/j0S5hvd.png)

* since it is in **μ** we will divide it by 1000000
* so 1000000/13.498 = 74085.049637
* I obviously used 74000 as bit rate, converted the hex to ascii with the tool itself and it gave me the flag:

```
CHTB{wh47?!_f23qu3ncy_h0pp1n9_1n_4_532141_p2070c01?!!!52}
```

