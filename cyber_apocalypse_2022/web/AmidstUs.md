---
layout: load_md
title: The White Circle | Cyber Apocalypse 2022 | Amidst Us Writeup
desc: Check out our writeup for Amidst Us for Cyber Apocalypse 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2022
parent: cyber_apocalypse_2022
category: web
challenge: Amidst Us
tags: "web, twh, starry, taz, ava, legend, ssrf"
date: 2022-05-21T00:00:00+00:00
last_modified_at: 2022-05-21T00:00:00+00:00
---


> Solved by : thewhiteh4t, Starry-Lord, Taz34, Avantika, Legend

![](https://i.imgur.com/r7mbpDi.png)

The downloadable files gives us a few clues in the files in requirements.txt and util.py:

* It’s a python environment
* It uses a vulnerable version of the python Pillow library

![](https://i.imgur.com/oeEjFBA.png)

https://github.com/python-pillow/Pillow/pull/5923

link: [python-pillow/Pillow#5923](https://github.com/python-pillow/Pillow/pull/5923)

This exchange provided some great insights on how to trigger SSRF

In util.py we find eval is being used on the data provided in the POST request, which allows us to upload a random image with background rgb parameters.

After a couple trial and errors we managed to grab the flag.txt and send it to our webhook by replacing one of the RGB values of “background”:

![](https://i.imgur.com/8kemcYF.png)

```
HTB{i_slept_my_way_to_rce}
```

