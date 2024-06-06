---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Hexahedron Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: crypto
challenge: Hexahedron
tags: "crypto, taz, rsa, rsactftool"
date: 2021-09-20T00:00:00+00:00
last_update: 2021-09-20T00:00:00+00:00
---

<h1 class="heading card-title white-text">Hacktivitycon 2021</h1>

## Hexahedron
> Solved by: Taz34


- We are give values of n,e and c.
- Decode them into simple numeric form using python.
![](https://i.imgur.com/z1yvvX8.png)

- it looks like RSA, we used RsaCtfTool to decipher it.

: https://github.com/Ganapati/RsaCtfTool


    python3 RsaCtfTool.py -n 112339816301925396926211289689793745814213925314273886071305785874178028552510482239036537066616690493241410015435402110525284201411608164205573122430898583517515498250410244592963132324072861567753086739636553410154316180827724708002409356129254383468446158145079982391991062389788544378839486986385137994309 -e 3 --uncipher 2217344750798178599616518881851238192046537371134831984828894413752520937378161486880269974456574131502921272953104454680926482208357166098075344508240480152890914678813031666242202555794691235412837030045499161787224264164243336308650477343133919653356349913604131486721125


![](https://i.imgur.com/vOzC4jK.png)


here we have the flag.