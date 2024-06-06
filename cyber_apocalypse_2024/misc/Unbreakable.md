---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Unbreakable Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: misc
challenge: Unbreakable
tags: "misc, twh, python, jail, escape"
date: 2024-03-16T00:00:00+00:00
last_update: 2024-03-16T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

## Unbreakable
> Solved by : thewhiteh4t

- This is a python jail challenge, at first it looks difficult because even built-ins cannot be used but then we realized two things :
    - it is using `eval` instead of `exec`
    - open() is not blocked
- Payload :

```
print(open('flag.txt').read())
```

![](https://i.imgur.com/gEM0du3.png)