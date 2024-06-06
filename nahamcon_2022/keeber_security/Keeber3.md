---
layout: load_md
title: The White Circle | Nahamcon 2022 | Keeber 3 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: keeber_security
challenge: Keeber 3
tags: "osint, starry, web"
date: 2022-05-03T00:00:00+00:00
last_update: 2022-05-03T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2022</h1>

## Keeber 3
> Solved by: Starry-Lord

![](https://i.imgur.com/nTVVuT3.png)

Here is their github:

https://github.com/keebersecuritygroup

https://github.com/keebersecuritygroup/security-evaluation-workflow/commit/e76da63337cfabb12ea127af3f86168e9dd08428


We can see at this point in time a file called asana_secret.txt was uploaded to the github by mistake, Tiffany made a typo in the .gitignore file which ended up preventing asana_secret.tx from being commited (which doesn’t exist).
Looking up Asana, I read we can query other users e-mails if we invite them to a group we create. It didn’t help us here but still noticeable detail.
Researching more on asana, I discovered it has an API which allows to get information back with the right Authorization Header.

![](https://i.imgur.com/qcH4mkz.png)

