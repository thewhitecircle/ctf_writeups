---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Target Practice Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: warmups
challenge: Target Practice
tags: "warmup, ava, stegano"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---

<h1 class="heading card-title white-text">Hacktivitycon 2021</h1>

## Target Practice
> Solved By : ava

We are given a GIF file, which actually works, and after opening we can see it has some sort of code on it, which changes pretty quick, so we need to split GIF into frames, I used https://ezgif.com/split to split and then downloaded all frames, and did a quick google re-image search on one of them, which result us in knowing that it is called MAXICODE, used by UPS.
then I found i MAXICODE decoder - https://products.aspose.app/barcode/recognize/maxicode#
and i had to manually check every image, which seems like not the intended way, but okie.
The 15th frame (if you started counting from 0) has the flag

`flag{385e3ae5d7b2ca2510be8ef4}`


