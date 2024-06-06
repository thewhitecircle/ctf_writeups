---
layout: load_md
title: The White Circle | Nahamcon 2021 | veebee Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: crypto
challenge: veebee
tags: "crypto, vbe, vbs"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## veebee

Visual Basic Script can be encoded and they become vbe files, there are some encoders in the wild, python one did not work but there is another decoder here which works : https://www.interclasse.com/scripts/decovbe.php

* put the code in a .vbs file
* launch a cmd and execute

```
wscript decode.vbs veebee.vbe
```

* a dialogue box will open with the flag in it

