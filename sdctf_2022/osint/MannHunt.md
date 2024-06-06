---
layout: load_md
title: The White Circle | Sdctf 2022 | Mann-Hunt Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2022
parent: sdctf_2022
category: osint
challenge: Mann-Hunt
tags: "osint, ava"
date: 2022-05-10T00:00:00+00:00
last_update: 2022-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2022</h1>

## Mann-Hunt
> Solved by: Avantika(iamavu)

```
Mann Hunt

We were on the trail of a notorious hacker earlier this week, but they suddenly went dark, taking down all of their internet presence...All we have is a username. We need you to track down their personal email address! It will be in the form ****.sdctf@gmail.com. Once you find it, send them an email to demand the flag!

Username
mann5549
```

We have a username `mann5549`, I checked in discord server, but couldn’t find anything

Let’s try to find social media accounts, usually I go with [instant-username-search](https://instantusername.com/#/)
From there I got the twitter - https://twitter.com/mann5549

![](https://i.imgur.com/2Qcpf1C.png)

Which had the following website - https://mann.codes, though I couldn’t really find much on that website, even checked in WayBack Machine
Coding reminded me of GitHub, so I went and did a GitHub search for `mann5549` but no such user, I though I should also query for the website name itself (`mann.codes`)
and I found the user. YAY.

![](https://i.imgur.com/M7J9LVR.png)

Visiting the repository, and going through commits and specifically this file 
`[src/components/seo.js](https://github.com/manncyber/manncodes.github.io/commit/e81f6315e6a1ecc2277246547f85f3c9e0ebf11e#diff-46bb1d99a93bc5b6f63d50361abac9cc4c09038b92b77536c85a93ff2f8fc401)`
I found a name 

![](https://i.imgur.com/pEHB3O9.png)

Let’s google this name and we get a LinkedIn the first search itself - 
https://www.linkedin.com/in/emanuel-hunt-34749a207/

![](https://i.imgur.com/OV9iUB7.png)

A google drive file, let’s see what it is - https://drive.google.com/file/d/10No4G_5iv2t5jxbvg2-weXkFouZrnQtg/view
No email sadly here D:
but then I remembered we can look up metadata of google drive file via google’s API and possibly get the email
let’s go to the Google API website - https://developers.google.com/drive/api/v3/reference/files/get

Reading the documentation, I realised we need the fileID, which is just present in the URL itself and we need to tell the API to show all possible field of metadata

![](https://i.imgur.com/eYGlnaq.png)


Hit execute and boom we get the email ID - `mann.sdctf@gmail.com`
Now send a email to ID asking for flag, and soon we get a autoreply with the flag

FLAG - `**sdctf{MaNN_tH@t_w@s_Ann0YinG}**`

P.S.:
Alternate way to check for docs email:

![](https://i.imgur.com/LaDilVF.png)

