---
layout: load_md
title: The White Circle | Sdctf 2021 | hIDe and seek 2 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2021
parent: sdctf_2021
category: osint
challenge: hIDe and seek 2
tags: "osint, ava, nigamelastic"
---

<h1 class="heading card-title white-text">Sdctf 2021</h1>

## hIDe and seek 2
> Solved By : Ava and nigamelastic

* I've gotten some more good intel. Apparently, the following information is the location of another flag!

```
First piece of info : gg/4KcDWnUYMs

Second piece of info : 810237829564727312-810359639975526490
```

* the first piece is obviously discord:
* on opening the channel we see

![](https://paper-attachments.dropbox.com/s_E1CDE00EEF36A360B08C6F7FBF981F7819B27F689743D6B8214DB48BA518E43A_1620452658182_image.png)

```
sdctf{m@st3R_h@Ck3R_9999}
```

* which seems to be flag format, I am guessing that starts with 1 till 9999.

**Rabbit Hole**

The second part is obviously a unix time stamp: 

```
810237829564727312 is Mon 4 September 1995 18:03:49.564 UTC

and

810359639975526490 is Wed 6 September 1995 03:53:59.975 UTC
```

* my guess was to check the chats between the times mentioned, but they aren't matching

* `sdctf{m@st3R_h@Ck3R_1995}` --> randomly tried its incorrect

* however thanks to Ava we found it was a rabbit hole! below is the correct way to solve it.

* The numbers actually specify the discord links
* usually the discord message links are something like this :
https://discord.com/channels/675369276403744776/782835136654737429/840472812690210826

* there are three numbers, i am just taking guess :
    * first number is for server
    * second seems for the channel 
    * third seems for message

* we copy link for any message from the server we found and replace the last two numbers with the one given to us in the challenge
* https://discord.com/channels/810237829564727308/810237829564727312/810359639975526490
* boom, we get the right message

```
sdctf{m@st3R_h@Ck3R_4807}
```

