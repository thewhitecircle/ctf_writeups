---
layout: load_md
title: The White Circle | Sdctf 2022 | Susan album party Writeup
desc: Check out our writeup for Susan album party for Sdctf 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Sdctf 2022
parent: sdctf_2022
category: forensics
challenge: Susan album party
tags: "forensics, starry"
date: 2022-05-10T00:00:00+00:00
last_modified_at: 2022-05-10T00:00:00+00:00
---



> Solved by: Starry-lord 

![](https://i.imgur.com/hueaTeT.png)

We get the file stub and notice its a jpeg image. Since it was pretty heavy, and this image looks like its very low res, it was almost sure there were stuff hidden inside. When opened into a image editor such as gimp, we see the first part of the flag:

![](https://i.imgur.com/4HWc10U.jpg)

This hinted at magic bytes for jpg files FFD8. At this point I turned the file to hex and looked for ffd8:

![](https://i.imgur.com/6BAkkUp.png)

and found three files like that:

![](https://i.imgur.com/U78QPer.jpg)

![](https://i.imgur.com/GJDjq5B.jpg)

![](https://i.imgur.com/T9IWd3d.png)

```
sdctf{FFD8_th3n_SOME_s7uff_FFD9} 'some' with a zero
```
