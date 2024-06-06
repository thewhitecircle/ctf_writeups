---
layout: load_md
title: The White Circle | Metasploit 2021 | 9 of diamonds Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: 9_of_diamonds
challenge: 9 of diamonds
tags: "web, twh"
date: 2021-12-08T00:00:00+00:00
last_modified_at: 2021-12-08T00:00:00+00:00
---

<h1 class="heading card-title white-text">Metasploit 2021</h1>
## 9 of diamonds

> Solved By : thewhiteh4t

```
Port : 8080
```

In this challenge we were presented with a website about cookies, obvious hint.
There were 3 sections :

- User Registration
- User Login
- Admin Login

First I registered a user account and checked the cookies, there were 4 cookies for our user :

- admin : false
- authenticated-user : true
- made-an-account : true
- visited-main-page : true

I just had to modify admin from false to true and send the request and we got the flag.

![](https://i.imgur.com/RxycAEW.png)