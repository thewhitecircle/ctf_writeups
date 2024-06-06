---
layout: load_md
title: The White Circle | Nahamcon 2022 | Mobilize Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: mobile
challenge: Mobilize
tags: "mobile, legend, android"
date: 2022-05-03T00:00:00+00:00
last_modified_at: 2022-05-03T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2022</h1>

## Mobilize
> Solved by: Legend

In this challenge an andorid APK was given.

Initially I installed the apk on android vritual device to check what’s the app is about and to know what’s happening with the app. But there was nothing informative.

![](https://i.imgur.com/cDBB9pM.png)

Then with the help of apktool I decompiled the apk to see what’s the functioning of the app and also to look for flag string.  Their were lot’s of sub-directories so I just used grep to see if the flag might be present in plain text, and got the flag.

![](https://i.imgur.com/PuimDJi.png)