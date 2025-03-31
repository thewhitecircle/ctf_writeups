---
layout: load_md
title: The White Circle | Nahamcon 2021 | intigriti Sponsor Writeup
desc: Check out our writeup for intigriti Sponsor for Nahamcon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: osint
challenge: intigriti Sponsor
tags: "osint"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---



https://app.intigriti.com/researcher/programs/intigriti/nahamcon2021ctf/detail

found this which has the following:

![](https://i.imgur.com/5IDtKf4.png)

the two links do not work  so we shall try deciphering this using :

https://enkhee-osiris.github.io/Decoder-JSFuck/

but we got an error stating about an illegal character

https://stackoverflow.com/questions/35657620/illegal-character-error-u200b

solution to this was easy as we just opened the challenge text in VSCode and found the `zero width space` character and removed it and the decoder now works!

