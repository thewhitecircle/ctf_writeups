---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Invitation Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: forensics
challenge: Invitation
tags: "forensics, starry, vba"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Invitation

> Solved By : Starry-Lord

* So we get a docm file. 
* I start by unzippping the word document 
* We get a docm
* Unzip it again and see folders

![](https://i.imgur.com/Q7ZRb7J.jpg)

**PART 1**

* First thing I tried to do after looking around was 

```bash
strings vbaProject.bin
```

* Which gives back interesting hex lines. 

![](https://i.imgur.com/knF1In2.jpg)

* Then decrypt from hex

![](https://i.imgur.com/RNhR8uO.jpg)

* From base64 urlsafe alphabet will show the following 

![](https://i.imgur.com/clunBcD.jpg)

```
CHTB{maldocs_are
```

**PART 2**

* Upload full vbaProject file this time and do the same as before. 

![](https://i.imgur.com/wgeYgSG.jpg)

* Use base64 urlsafe alphabet
* We get second part of the flag by reversing

```
_the_new_meta}
```
```
CHTB{maldocs_are_the_new_meta}
```

