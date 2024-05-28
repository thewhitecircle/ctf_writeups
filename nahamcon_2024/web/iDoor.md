---
layout: load_md
title: The White Circle | Nahamcon 2024 | iDoor Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: iDoor
tags: "web, legend, idor"
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>


## iDoor
> Solved by: Legend


- This one also is very clear with the name that we need to find the IDOR. And the challenge description has given the hint that they are using `SHA256` algorithm to create the reference objects.


![](https://i.imgur.com/mLRT5Fy.png)

- We are a customer with `ID: 11` and reference id is `4fc82b26aecb47d2868c4efbe3581732a3e7cbcc6c2efb32062c08170a05eeb8`


- I confirmed that `11`'s refence object will give the same hash if encoded with `SHA256` or not. We can use `openssl` or any other relevant tool.
![](https://i.imgur.com/BABNf8E.png)

- The hash was same so I started with `0` and replaced my hash with new one.
![](https://i.imgur.com/CDT4BWw.png)

- Using `0` only gave us the hash `5feceb66ffc86f38d952786c6d696c79c2dbc239dd4e91b46729d73a27fb57e9` which got us the flag.
![](https://i.imgur.com/ykIL1iO.png)

```
flag{770a058a80a9bca0a87c3e2ebe1ee9b2}
```

