---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Dragon Fury Writeup
desc: Check out our writeup for Dragon Fury for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: coding
challenge: Dragon Fury
tags: "coding, twh, python"
date: 2025-03-29T00:00:00+00:00
last_modified_at: 2025-03-29T00:00:00+00:00
---



> Solved by thewhiteh4t


- Simulate the battle by computing the total damage dealt over successive rounds until victory is achieved

```
import json
import random

input_text = json.loads(input())
T = int(input())
num_sublists = len(input_text)

def magic():
    rand_t = 0
    choices = []
    for sublist in input_text:
        rand = random.choice(sublist)
        choices.append(rand)
        rand_t += rand
    return rand_t, choices

random_sum = 0
flag_choices = None

while random_sum != T:
    random_sum, flag_choices = magic()

print(flag_choices)
```

![](https://i.imgur.com/2po9B6B.png)

