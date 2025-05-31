---
layout: load_md
title: The White Circle | Nahamcon 2025 | Method in the Madness Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: Method in the Madness
tags: "web, twh, http methods"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>

## Method in the Madness
> Solved by thewhiteh4t

Six checkboxes are displayed, when we click on `/interesting` one checkbox is ticked. Goal was to tick all checkbox and hope for it to reveal the flag.

Hint is in the name of the challenge : Method → HTTP Methods

So we just have to try different methods on `/interesting`


    curl http://challenge.nahamcon.com:31661/interesting
    curl -X POST http://challenge.nahamcon.com:31661/interesting
    curl -X PUT http://challenge.nahamcon.com:31661/interesting
    curl -X PATCH http://challenge.nahamcon.com:31661/interesting
    curl -X DELETE http://challenge.nahamcon.com:31661/interesting
    curl -X OPTIONS http://challenge.nahamcon.com:31661/interesting

After running all curl command, flag appears!


![](https://i.imgur.com/AtuE3fG.png)


**Key Learning and Takeaways**


- Beyond Just GET and POST : We all know GET and POST methods, but there are more HTTP methods such as :
    - PUT (for creating or updating resources)
    - PATCH (for partial updates)
    - DELETE (for removing resources)
    - OPTIONS (for asking the server what methods it supports)
- This challenge was a perfect example of how foundational web knowledge combined with simple tools can show new things while testing.


