---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Reactor Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: mobile
challenge: Reactor
tags: "mobile, starry"
---

<h1 class="heading card-title white-text">Hacktivitycon 2021</h1>

## Reactor
> Solved by: Starry-Lord


Flag gets more and more unscrambled with correct digits. 
Basically 4 digit Pin probabilities plus dynamic deobfuscating made the total of possibilities go down to less than 40, like an Eval situation, where you would have result if your first characters are correct. 

A. Input 1 digit, 0 to 5, (5)
B. Input second digit 0-9(9)
C. Input third digit 0-2(2)
D. Input fourth digit 0-7(flag!)

27 tries on /40

I agree it's most likely not the intended way but 4 digits pin plus Eval like function is vulnerable enough 😉

![](https://i.imgur.com/zkFlMup.jpg)


5 was the only one starting with letter f, four characters and a promising { like in other flags. 

![](https://i.imgur.com/k1LEdFA.jpg)

![](https://i.imgur.com/i0ZIN3Z.jpg)

![](https://i.imgur.com/dVezqS5.jpg)

![](https://i.imgur.com/odN6hY0.jpg)


