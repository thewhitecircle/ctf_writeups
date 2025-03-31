---
layout: load_md
title: The White Circle | Nahamcon 2024 | All About Robots Writeup
desc: Check out our writeup for All About Robots for Nahamcon 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: All About Robots
tags: "web, legend"
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---



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

