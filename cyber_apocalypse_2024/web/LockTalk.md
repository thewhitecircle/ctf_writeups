---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | LockTalk Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: web
challenge: LockTalk
tags: "web, twh, starry, haproxy, cve"
date: 2024-03-16T00:00:00+00:00
last_update: 2024-03-16T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

## LockTalk
> Solved by : thewhiteh4t & Starry-lord

This challenge came with files and a docker instance.

![deny if /api/v1/get_ticket](https://i.imgur.com/BA10qeR.png)

We can see the haproxy configuration is set to deny access to the /api/v1/get_ticket endpoint, and that the requirements for this webpage to work is an outdated python_jwt 3.3.3 library.

![Enumeration!](https://i.imgur.com/NuH8e6b.png)

When we visit the webpage, we are greeted with 3 possible endpoints making everything pretty clear for our next step. but we can’t get a token when we try it because of haproxy. But when we send the request to BurpSuite’s repeater tab, and modify the path, we can actually get a ticket.

![bypass haproxy](https://i.imgur.com/lgQWfIy.png)

With our ticket we can now try the other 2 endpoints and read some exchanges between a fake ransomware group named `Openbit3.0` and some of their victims, as a reference to the recent Operation of the Cronos Taskforce related to the LockBit ransomware gang.

![ransom chats](https://i.imgur.com/hCVlziR.png)

So it was time to look into that old python json web token library, and sure enough, there was an exploit to be found. This CVE from 2022 allows to send and claims we want by exploiting the way the json web token is parsed.


> python-jwt is a module for generating and verifying JSON Web Tokens. Versions prior to 3.3.4 are subject to Authentication Bypass by Spoofing, resulting in identity spoofing, session hijacking or authentication bypass. An attacker who obtains a JWT can arbitrarily forge its contents without knowing the secret key. Depending on the application, this may for example enable the attacker to spoof other user's identities, hijack their sessions, or bypass authentication. Users should upgrade to version 3.3.4. **There are no known workarounds.**
> https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-39227

What a nice thing to read as a pentester, “there are no known workarounds”.


https://github.com/user0x1337/CVE-2022-39227


With the help of the given files we can determine that `administrator` is the required role to bypass all restrictions on the page.


![roles: guest, administrator](https://i.imgur.com/wtfwDuS.png)


With this knowledge in hand, we can now use the github proof of concept, as shown below.


![claim we are admin](https://i.imgur.com/8ZDg6Tr.png)


With the valid token we retrieved, and the correct role name, we can now craft a payload and send it to the /api/v1/flag endpoint.


![](https://i.imgur.com/X6jWB1g.png)


```
HTB{h4Pr0Xy_n3v3r_D1s@pp01n4s}
```

