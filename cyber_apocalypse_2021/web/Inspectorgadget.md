---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Inspector gadget Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: web
challenge: Inspector gadget
tags: "web, bobby, ava, js"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Inspector gadget

> Solved by: Bobby sox and ava

* Visiting the webpage we see a part of a flag: CHTB{

* If we keep looking around the pages, we find in /static/js/main another part of the flag:

```
us3full_1nf0rm4tion}
```

* This in combination with the flag on the website is not the correct flag so far.
* static/css/main.css has another potential hint at the top with: c4n_r3ve4l_ 
* so, so far we have collected 3 pieces of a flag:

```
1. CHTB{
2. us3full_1nf0rm4tion}
3. c4n_r3ve4l_ 
```

* in js.main we will also find the last piece of our flag:1nsp3ction_
* the full flag ended up being :`CHTB{1nsp3ction_c4n_r3ve4l_us3full_1nf0rm4tion}`

