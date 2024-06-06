---
layout: load_md
title: The White Circle | Metasploit 2021 | ace of hearts Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: ace_of_hearts
challenge: ace of hearts
tags: "web, starry"
date: 2021-12-08T00:00:00+00:00
last_modified_at: 2021-12-08T00:00:00+00:00
---

<h1 class="heading card-title white-text">Metasploit 2021</h1>
## ace of hearts

> Solved by: Starry-Lord

```
Port 20011
```

We had a image gallery here with 4 user galleries. 

Sarah’s gallery
John’s gallery
Ripley’s gallery
Ash’s gallery

John’s gallery was not accessible, set to private.

When querying any other gallery, we could see the URL changing like so:

```
http://172.17.15.117:20011/gallery?galleryUrl=/admin
```

I tried to call the admin gallery through local-host and got access to the admin panel, where I just had to untick a box for making John’s gallery public.

```
http://172.17.15.117:20011/gallery?galleryUrl=http://127.0.0.1:20011/admin
```

He had this flag inside:

![](https://i.imgur.com/Dnqy853.png)

> Yay an Ace!!!
>   — Starry-Lord