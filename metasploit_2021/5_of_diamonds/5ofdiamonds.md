---
layout: load_md
title: The White Circle | Metasploit 2021 | 5 of diamonds Writeup
desc: Check out our writeup for 5 of diamonds for Metasploit 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: 5_of_diamonds
challenge: 5 of diamonds
tags: "web, starry, twh, bobby, sql, sqli"
date: 2021-12-08T00:00:00+00:00
last_modified_at: 2021-12-08T00:00:00+00:00
---


> Solved by: Starry-Lord, Thewhiteh4t, Bobbysox

```
Port 11111
```

This was an SQL Injection vulnerability. We had many problems for this one in terms of stability, in fact only one could attack it with sqlmap at the time.

We managed to get the password out from the database, which was a very long alpha-numeric string, bigger than max amount of chars accepted by the input field on the login page.

Passing the password with burp solved the problem and displayed a link to flag.png

### Afterthought

We could bypass login through the username field with 

```
username=X'or 1=1 --
password=notThePassword
```

but i missed that we could also simply bypass authentication with the password field:

```
username=admin
password='or 1=1 --
```

![](https://i.imgur.com/PHMf8sD.png)