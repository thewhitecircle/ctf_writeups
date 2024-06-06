---
layout: load_md
title: The White Circle | Nahamcon 2021 | Lyra and Orion Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: mission
challenge: Lyra and Orion
tags: "web, git, osint"
date: 2021-03-15T00:00:00+00:00
last_update: 2021-03-15T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## Lyra and Orion

if we check the “meet the team” commit we can see names of all employees!

```
git show 4c88ac1c56fe228267cf415c3ef87d7c3b8abd60
```

![](https://i.imgur.com/uZKpNSE.png)

```
Orion Morra
Lyra Patte
Gemini Coley
Vela Leray
Pavo Welly
```

On twitter we can find Lyra’s profile : https://twitter.com/LyraPatte/with_replies

and she has linked one of the website links : https://constellations.page/constellations-documents/1/

`/1/` lets try more…on `/5/`

![](https://i.imgur.com/jRl29Un.png)

Once again on twitter we can find orion at : https://twitter.com/OrionMorra/with_replies

he has posted two useful pictures

https://twitter.com/OrionMorra/status/1363789936219082756/

AND

https://twitter.com/OrionMorra/status/1370730836736274433/


```
flag{0bcffb17cbcbf4359a42ec45d0ccaf2
```

```
orion:stars4love4life
```

another set of creds

