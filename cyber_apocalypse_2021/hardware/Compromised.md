---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Compromised Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: hardware
challenge: Compromised
tags: "hardware, nigamelastic, salea"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Compromised

> Solved by : Nigamelastic

* import the file in salea logic analyzer, and use i2c analyzer
* export the data and you will see two columns if we take everything written and try the  hex dump we get

```
set_maCxH_lTimB{itn_tuo1:110_se73t_2mimn1_nli4mi70t_2to5:_1c0+.]<+/4~nr^_yz82Gb3b"4#kU_..4+J_5.
```
```
3M.2B1.4B.1dV_5. yS.5B7k3..1V.Qxm.!j.@Q52yq)t%# @5%md}S.
```

* and we can see its slightly off i noticed the following :

![](https://i.imgur.com/y8YiO2F.png)

* so i used only `0x2C` used the corresponding hex 

which is 

```
0x43 0x48 0x54 0x42 0x7B 0x6E 0x75 0x31 0x31 0x5F 0x37 0x33 0x32 0x6D 0x31 0x6E 0x34 0x37 0x30 0x32 0x35 0x5F 0x63 0x34 0x6E 0x5F 0x38 0x32 0x33 0x34 0x6B 0x5F 0x34 0x5F 0x35 0x33 0x32 0x31 0x34 0x31 0x5F 0x35 0x79 0x35 0x37 0x33 0x6D 0x21 0x40 0x35 0x32 0x29 0x23 0x40 0x25 0x7D
```

* and got the flag 

```
CHTB{nu11_732m1n47025_c4n_8234k_4_532141_5y573m!@52)#@%}
```