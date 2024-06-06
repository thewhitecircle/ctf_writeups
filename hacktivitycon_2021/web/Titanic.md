---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Titanic Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: web
challenge: Titanic
tags: "web, twh"
date: 2021-09-20T00:00:00+00:00
last_update: 2021-09-20T00:00:00+00:00
---

<h1 class="heading card-title white-text">Hacktivitycon 2021</h1>

## Titanic
> Solved By : thewhiteh4t


- In this challenge we were given a website of a company
- Two things which instantly caught attention were `URL Capture` and `Admin` buttons
- URL capture service accepts a URL and takes screenshot of the webpage


![](https://i.imgur.com/Zvb3jw1.png)



- Admin page got a login
- First idea was to try `http://localhost` and it worked


![](https://i.imgur.com/p1p8cwY.png)

![](https://i.imgur.com/jBMQpgG.png)

- This is same as the loading splash screen I saw while loading the challenge website
- Next I checked `robots.txt` and got 200 and this revealed a new path `/server-status`


![](https://i.imgur.com/MvvLwjj.png)



- Next I obviously tried to access `/server-status` and got 200 again


![](https://i.imgur.com/aIKbkBX.png)

- And in the logs you can see the login credentials!


![](https://i.imgur.com/QDhwPpP.png)

