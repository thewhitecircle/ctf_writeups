---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Unbreakable Writeup
desc: Check out our writeup for Unbreakable for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: misc
challenge: Unbreakable
tags: "misc, twh, python, jail, escape"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by : thewhiteh4t

- This is a python jail challenge, at first it looks difficult because even built-ins cannot be used but then we realized two things :
    - it is using `eval` instead of `exec`
    - open() is not blocked
- Payload :

```
print(open('flag.txt').read())
```

![](https://i.imgur.com/gEM0du3.png)