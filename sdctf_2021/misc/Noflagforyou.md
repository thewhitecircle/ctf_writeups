---
layout: load_md
title: The White Circle | Sdctf 2021 | No flag for you Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2021
parent: sdctf_2021
category: misc
challenge: No flag for you
tags: "misc, taz, jail, escape"
date: 2021-05-10T00:00:00+00:00
last_update: 2021-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2021</h1>

## No flag for you
> Solved By : Taz

* We found out that `ls`, `cat`, `echo` commands were available.
* So started looking around for these.
* Found a way to list dir using echo:

```
echo /*
```

![](https://i.imgur.com/nzyMsSw.png)

* started looking around for the flag and found it in the `/home/user/run/opt` dir

```
echo /home/user/run/opt/*
```

* Did some research on ways to read a file using echo, and got something.
* Reference link: https://stackoverflow.com/questions/22377792/how-to-use-echo-command-to-print-out-content-of-a-text-file

```
echo "$(</home/user/run/opt/flag-b01d7291b94feefa35e6.txt)"
```

![](https://i.imgur.com/pPB49gk.png)

