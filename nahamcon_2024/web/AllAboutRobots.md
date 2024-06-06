---
layout: load_md
title: The White Circle | Nahamcon 2024 | All About Robots Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: All About Robots
tags: "web, legend"
date: 2024-05-28T00:00:00+00:00
last_update: 2024-05-28T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>


## All About Robots
> Solved by: Legend


- This challenge is clear with name that we need to check `robots.txt` file. Even the homepage contained images showing `robots`.
![](https://i.imgur.com/u13IuiO.png)

- So first thing I visited the file and got one `Disallow` path.
![](https://i.imgur.com/laHlqTI.png)

- After visiting `/open_the_pod_bay_doors_hal_and_give_me_the_flag.html`, I got the flag.
![](https://i.imgur.com/oMT5W03.png)

```
flag{3f19b983c1de42bd49af1a237d7e57b9}
```

