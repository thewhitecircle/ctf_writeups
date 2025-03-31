---
layout: load_md
title: The White Circle | Metasploit 2021 | 9 of diamonds Writeup
desc: Check out our writeup for 9 of diamonds for Metasploit 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: 9_of_diamonds
challenge: 9 of diamonds
tags: "web, twh"
date: 2021-12-08T00:00:00+00:00
last_modified_at: 2021-12-08T00:00:00+00:00
---


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