---
layout: load_md
title: The White Circle | Nahamcon 2024 | The Mission Part 1 - Knock Knock Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: The Mission Part 1 - Knock Knock
tags: "web, starry, api"
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>

## The Mission Part 1 - Knock Knock
> Solved by: Starlord

Mirror : <a href="https://synthweb.ch/blog/nahamcon-ctf-2024/the-mission-part-1-knock-knock">https://synthweb.ch/blog/nahamcon-ctf-2024/the-mission-part-1-knock-knock</a>


This challenge was the first of 7 challenges on the same blackbox, which meant we had permission to fuzz and bruteforce if needed.

Upon connecting to the challenge, we are redirected to the url: `/app/login` , which shows a register button as well. Registering a user was an interesting task, as I could not register from any other domain than `nahamcon.ctf`. This was a bummer since the app required validation by mail to activate the user account just created.


![validation required](https://i.imgur.com/CiCl37a.png)


Upon further enumeration, I noticed `/app/logout` was redirecting to `/dev-app`, which opened a new avenue for my progress. 

![finding enpoints](https://i.imgur.com/48qm0MO.png)


Registering a user there proved more successful and didn’t have validation, which gave me a cookie named `dev-token`. It took me quite some time before realising that the logout option, if used on the `/app` side, would actually send a delete request for the correct cookie name for production.


![production cookie name](https://i.imgur.com/EPaJ6f8.png)


With the correct cookie name, it was then possible to simply reuse the jwt cookie obtained on the dev-app and reuse it in the production app, by changing its name in the browser tools or burp.

```
token-for-prod=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjozfQ.MVXc0x_5qMYMmU3orSxp7HfC1FOB9FXuutII-AP_eno
```

With that in hand, we now can be identified as an authenticated user in the dashboard, even though it’s unavailable for maintenance.

![authentication succesful](https://i.imgur.com/bP1aHY4.png)


Expanding on previous findings, since we now had a username, I wanted to see his details so I visited the following and caught the flag!

```
/app/api/me
```

![flag found](https://i.imgur.com/3KT3Fb1.png)

```
flag{925f2f867c0f46d986979c935aafca31}
```

