---
layout: load_md
title: The White Circle | Uiuctf 2021 | Chaplin's PR Nightmare - 8 Writeup
desc: Check out our writeup for Chaplin's PR Nightmare - 8 for Uiuctf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Uiuctf 2021
parent: uiuctf_2021
category: osint
challenge: Chaplin's PR Nightmare - 8
tags: "osint, starry"
date: 2021-08-12T00:00:00+00:00
last_modified_at: 2021-08-12T00:00:00+00:00
---


> Solved by : Starry-Lord

Straightup doxx Charlie by finding the email he set all these accounts up with, and investigate it.
The inner content of this flag begins with **"b0"**


So now to find Charlie Chaplin dev 's e-mail address I used github. 

https://github.com/charliechaplindev/C3D-Official/commit/ce81ede5ab18b6f4ca32ace4359c5570954dfc9b.patch

Remove the dot patch extension for regular layout. 

Then I used the good old article https://medium.com/week-in-osint/getting-a-grasp-on-googleids-77a8ab707e43

And found contributions on Google maps at this location :

https://www.google.com/maps/contrib/117833363030761934622/photos/@41.9667449,-87.6385934,3a,84.8y,90t/data=!3m7!1e2!3m5!1sAF1QipOyi7R_ApFvR6ZeSYVpLYt4p_2wDjLu6r9V3T3T!2e10!6shttps:%2F%2Flh5.googleusercontent.com%2Fp%2FAF1QipOyi7R_ApFvR6ZeSYVpLYt4p_2wDjLu6r9V3T3T%3Dw545-h260-k-no!7i956!8i456!4m3!8m2!3m1!1e1

![](https://i.imgur.com/lGLBAwA.jpg)



    uiuctf{b0rk_b0rk_1_lOv3_mY_d0g<3}


