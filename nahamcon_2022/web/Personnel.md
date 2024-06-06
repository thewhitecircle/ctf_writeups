---
layout: load_md
title: The White Circle | Nahamcon 2022 | Personnel Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: web
challenge: Personnel
tags: "web, nigamelastic"
date: 2022-05-03T00:00:00+00:00
last_update: 2022-05-03T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2022</h1>

## Personnel
> Solved By : nigamelastic

The Challenge contains a python file,
On opening the python file u can clearly see som regex fu going on:

![](https://i.imgur.com/mvSfYju.png)

going through the code and testing it on the live link, u can see that it ignores the first letter if its upper case, and makes a logic around it. Its better if u view it in a debugging tool , I use regex101

![](https://i.imgur.com/XuhDSU4.png)

I also went through the official documentation (https://docs.python.org/3/library/re.html) to see if there are any special characters that can be used. the most interesting one was `|`
As per the documentation:

```
|
A|B, where A and B can be arbitrary REs, creates a regular expression that will match either A or B. An arbitrary number of REs can be separated by the '|' in this way. This can be used inside groups (see below) as well. As the target string is scanned, REs separated by '|' are tried from left to right. When one pattern completely matches, that branch is accepted. This means that once A matches, B will not be tested further, even if it would produce a longer overall match. In other words, the '|' operator is never greedy. To match a literal '|', use \|, or enclose it inside a character class, as in [|].
```

Once u go through it with a `flag{randomString}` u will find that the following regex would allow it. 

![](https://i.imgur.com/DN7cuJb.png)

So now we remove our initial regex and just add the ones we used aka `|flag{.*}|`
which gives u the flag

![](https://i.imgur.com/DrWLAJy.png)