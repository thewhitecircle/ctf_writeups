---
layout: load_md
title: The White Circle | Sdctf 2022 | Bishop Duel Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2022
parent: sdctf_2022
category: misc
challenge: Bishop Duel
tags: "misc, taz"
date: 2022-05-10T00:00:00+00:00
last_update: 2022-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2022</h1>


## Bishop Duel
> Solved by: Taz34

We have a chess board and we are playing as a white bishop and opponent as a black bishop.
So for us to lose or win this game we need to be running our bishop on the same color, i.e. as in the image below we can see the opponent ‘BB’ can move on ‘__’ spaces and we ‘WW’ can move on ‘\\\\’ spaces, so we need our bishop to move on ‘__’ spaces.

![](https://i.imgur.com/WA1zPxW.png)

So for that we need to get out of the board and there are tow ways out of the board i.e. the top right corner or the bottom left corner.
So, I decided to move out of the top right.
I used C1 then E2 and then we landed on a ‘__’ space.

![](https://i.imgur.com/uaalQSO.png)

Now just get in the way of the opponent to lose to get the flag.

![](https://i.imgur.com/60Ofz10.png)

```
Flag: sdctf{L0SiNG_y0uR_S0uRC3_C0d3_sUcKs}
```