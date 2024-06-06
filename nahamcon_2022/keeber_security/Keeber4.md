---
layout: load_md
title: The White Circle | Nahamcon 2022 | Keeber 4 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: keeber_security
challenge: Keeber 4
tags: "misc, starry, keepass"
date: 2022-05-03T00:00:00+00:00
last_modified_at: 2022-05-03T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2022</h1>

## Keeber 4
> Solved by: Starry-Lord

![](https://i.imgur.com/gNzQkHh.png)


To open this kbdx file we can use keepass2 on Kali Systems, after grabbing it from the github. 

To make a custom wordlist from public facing information I used cewl:

```
cewl https://raw.githubusercontent.com/keebersecuritygroup/security-evaluation-workflow/main/code_reviews.txt > code_reviews.txt 
```

I did so for each text files in the /security-evaluation-workflow/ repository.

Then I had to turn it into a crackable format with keepass2john:

```
starlord@HAL-9000:~/Bureau/Fun/Nahamcon2022/Keeber$ keepass2john ksg_passwd_db.kdbx 

ksg_passwd_db:$keepass$*2*58823528*0*d1aa5a09ccf3f75d30ea2d548ca045d28252c90adc8bf016bd444cbb3d6d5f65*580f6c41d95ea9407da649ee0312209f1686edf0b779458d57288ed7043c60ff*aec6b24ac45bf46d4b632d5e408799c7*4fa205b599089f79005e176c9c47690ffc58492169309a47613d4269a8ef2a52*f51a2a1f36f1ca1d10439aa78eccece46337274880f594f5a62a703f6007374f
```

![](https://i.imgur.com/kclUiYV.png)

```
password: craccurrelss
```

![](https://i.imgur.com/mH7mPvb.png)

![](https://i.imgur.com/AXs6oPQ.png)

