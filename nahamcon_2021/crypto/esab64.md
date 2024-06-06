---
layout: load_md
title: The White Circle | Nahamcon 2021 | esab64 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: crypto
challenge: esab64
tags: "crypto"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## esab64 

Its base64 backwards

initial string in file: `mxWYntnZiVjMxEjY0kDOhZWZ4cjYxIGZwQmY2ATMxEzNlFjNl13X`

The name is backwards so i reversed the string to:

`X31lNjFlNzExMTA2YmQwZGIxYjc4ZWZhODk0YjExMjViZntnYWxm`
 
base64 decode to: `_}e61e711106bd0db1b78efa894b1125bf{galf`
 
reverse the string once again for flag: `flag{fb5211b498afe87b1bd0db601117e16e}_`

