---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Bad Words Writeup
desc: Check out our writeup for Bad Words for Hacktivitycon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: misc
challenge: Bad Words
tags: "misc, bobby, bash, jail, escape"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---




> Solved By : bobbysox

This challenge was interesting. 99% of characters that I entered were flagged as “Bad Words”
After some trial and error, i got the first message down below “bash: fg: no job control”

I tried to implement some job control with no luck. However, when trying this, I noticed it processed “#! /bin/bash” without throwing errors. It processes the “/” character!!!! This made me take a step back and think about what were in. We’re in a custom restricted shell. In the past ive usually used native binaries to escape such situations. We have two options here: prefix our commands with “/”, or, try and call native binaries since we know we can just call any path. The latter seemed like the best solution. Call /bin/bash and see what happens. It worked!!! yay!


![](https://i.imgur.com/Hh86GbW.png)



![](https://i.imgur.com/KGGf8AK.png)






