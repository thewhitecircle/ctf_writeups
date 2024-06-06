---
layout: load_md
title: The White Circle | Hsctf 2021 | c-brother-1 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hsctf 2021
parent: hsctf_2021
category: misc
challenge: c-brother-1
tags: "misc, twh, osint"
date: 2021-06-20T00:00:00+00:00
last_update: 2021-06-20T00:00:00+00:00
---

<h1 class="heading card-title white-text">Hsctf 2021</h1>

## c-brother-1
> Solved By : thewhiteh4t

- The challenge mentions a user like `AC01010` and `JC01010` in the HSCTF discord server
- Finding the user was easy :

![](https://i.imgur.com/bqNUuM3.png)

- In discord users can add some links of their social media for other people to see, here I found the YouTube channel of `BC01010` :

![](https://i.imgur.com/ebXCPIe.png)

- There are no videos in the channel so we cannot see the watermark directly
- At first we thought that we can get it by using the YouTube API but it only offers to set or remove a watermark from our own profile
- So I searched for channels which use watermark in their videos and landed on `Motherboard`

![](https://i.imgur.com/6j6Jqf0.png)

- Motherboard uses a `subscribe` image as their watermark, next I checked the files loaded in YouTube to check if this image was also loaded :

![](https://i.imgur.com/WWhm3sI.png)

- Now we have the URL through which the watermark is being loaded :

```
https://i.ytimg.com/an/B6PV0cvJpzlcXRG7nz6PpQ/featured_channel.jpg
```

- We know that each channel as a random alphanumeric ID so I compared this URL with the channel page URL :

```
Channel   : https://www.youtube.com/channel/UCB6PV0cvJpzlcXRG7nz6PpQ
Watermark : https://i.ytimg.com/an/B6PV0cvJpzlcXRG7nz6PpQ/featured_channel.jpg
```

- If you noticed, in the watermark URL the ID is missing two characters from the start i.e. `UC`
- Following the pattern I tried to input the channel ID of `BC01010` without the first two characters and got the watermark!

```
Channel   : https://www.youtube.com/channel/UCqZq81jZcdjAHQJ3UtAbdaA
Watermark : https://i.ytimg.com/an/qZq81jZcdjAHQJ3UtAbdaA/featured_channel.jpg
```

![](https://i.imgur.com/q0B8qxd.png)

