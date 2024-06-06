---
layout: load_md
title: The White Circle | Sdctf 2022 | Samuel Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2022
parent: sdctf_2022
category: osint
challenge: Samuel
tags: "osint, ava, starry"
date: 2022-05-10T00:00:00+00:00
last_update: 2022-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2022</h1>


## Samuel
> Solved by : Avantika(iamavu) and Starry-Lord

![](https://i.imgur.com/ecBWaBK.jpg)

We have a youtube video https://www.youtube.com/watch?v=fDGVF1fK1cA. We can see a blurry video, with a beacon going on and off in the night, with what seems like cable-cars or planes in the distance. Short light and long lights seemed like morse code. Here's the decoded morse. 

```
WHWHWHGODWROUGHT
```

https://www.history.com/.amp/this-day-in-history/what-hath-god-wrought

According to the link, Samuel Morse demonstrated the telegraph in 1844 with the sentence "what had God wrought". 

So we are looking for a beacon that sends the message in morse code, and I found this https://sculpturemagazine.art/mixed-messages-mark-bradfords-what-hath-god-wrought/

Avantika then found the location for this place on Google maps. 
https://www.google.com/maps/place/What+Hath+God+Wrought/@32.8752134,-117.2429636,17z/data=!3m1!4b1!4m5!3m4!1s0x80dc07e0d30e81a7:0x69087278617d6b1d!8m2!3d32.8752134!4d-117.2407749

```
sdctf{32.875,-117.240}
```

