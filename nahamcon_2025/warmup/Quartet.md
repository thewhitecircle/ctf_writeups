---
layout: load_md
title: The White Circle | Nahamcon 2025 | Quartet Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: warmup
challenge: Quartet
tags: "warmup, twh, archive"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>


## Quartet
> Solve by thewhiteh4t

Four files are given with `.z01` extension, running `file` command on them shows the following output : 


    quartet.z01:  Zip multi-volume archive data, at least PKZIP v2.50 to extract
    quartet.z02:  data
    quartet.z03:  data
    quartet.z04:  Zip archive data, made by v3.0 UNIX, extract using at least v2.0, last modified, last modified Sun, May 10 2025 04:28:04, uncompressed size 2035495, method=deflate

Unzipping them leads to another file named `quartet.jpeg`. By running `strings` command on the image and grepping for flag string we can get the flag : 


    strings quartet.jpeg | grep flag
    flag{8f667b09d0e821f4e14d59a8037eb376}

**Key Learning & Takeaways**


- Run strings : You'd be surprised how often a flag or a crucial hint is just sitting there in plain text.
- Check the file type with file : A simple file command can tell you a lot about what you're dealing with. Knowing this helps you pick the right tools for the job.
- Keep an eye out for flag formats : Most CTFs use a consistent flag format (like `flag{...}`).


