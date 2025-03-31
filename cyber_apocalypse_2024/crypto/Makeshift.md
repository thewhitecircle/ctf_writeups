---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Makeshift Writeup
desc: Check out our writeup for Makeshift for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: crypto
challenge: Makeshift
tags: "crypto, legend, python"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by: Legend

Challenge description:

```
Weak and starved, you struggle to plod on. Food is a commodity at this stage, but you can’t lose your alertness - to do so would spell death. You realise that to survive you will need a weapon, both to kill and to hunt, but the field is bare of stones. As you drop your body to the floor, something sharp sticks out of the undergrowth and into your thigh. As you grab a hold and pull it out, you realise it’s a long stick; not the finest of weapons, but once sharpened could be the difference between dying of hunger and dying with honour in combat.
```

For this challenge we are given the code in which the flag is just getting shifted. So I just reversed the script with trail and error I got the flag.

```python
    #! /usr/bin/python3
    
    new_flag = "!?}De!e3d_5n_nipaOw_3eTR3bt4{_THB"
    
    flag = ''
    
    for i in range(0, len(new_flag), 3):
        flag += new_flag[i+2]
        flag += new_flag[i]
        flag += new_flag[i+1]
    
    flag = flag[::-1]
    
    print(flag)
```

```
HTB{4_b3tTeR_w3apOn_i5_n3edeD!?!}
```

