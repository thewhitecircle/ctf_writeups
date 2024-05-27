---
layout: load_md
title: The White Circle | Sdctf 2021 | GETS Request Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2021
parent: sdctf_2021
category: web
challenge: GETS Request
tags: "web, twh"
---

<h1 class="heading card-title white-text">Sdctf 2021</h1>

## GETS Request
> Solved By : thewhiteh4t

* The challenge hints at memory issues
* we can enter an integer and the web app sends a reply
* there are few checks which the web app makes:

```javascript
if(!req.query.n) {
    res.status(400).send('Missing required parameter n');
    return;
}
```
* so `n` cannot be blank

```javascript
const BUFFER_SIZE = 8;

if(req.query.n.length > BUFFER_SIZE) {
    res.status(400).send('Requested n too large!');
    return;
}
```

* so max length of `n` can be `8`
* the web app does not check for duplicate parameters, so we can send another n along with the first

![](https://i.imgur.com/wyyLaq7.png)

