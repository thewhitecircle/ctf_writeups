---
layout: load_md
title: The White Circle | Nahamcon 2025 | The Martian Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: misc
challenge: The Martian
tags: "misc, twh, binwalk, binary"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>


## The Martian
> Solved by thewhiteh4t

In this challenge we get a binary file. Running `file` command did not help because magic bytes of header and footer were corrupt. So I tried to use `hexdump -C` command to check its contents and magic bytes. At the time of solving idea was that most probably multiple files were concatenated into one file, so I used `binwalk` to extract available files :


    binwalk -e challenge.martian


![](https://i.imgur.com/x92plLw.png)


So binwalk found multiple `bzip` archives and extracted them in one go, in the next image we can see the extracted directories and inside one of the directories we get a `decompressed.bin`, this is the default naming convention of binwalk so to find actual type of the file we can again use `file` command, and we can see that it's a `JPEG` image


![](https://i.imgur.com/7CIe5IO.png)


So I renamed the file and opened it in a gallery viewer : 


![](https://i.imgur.com/liCEDQ3.jpeg)


**Key Learning and Takeaways**


- Tools complexity : Our first instinct is always using the `file` command, but when it did not work we switched to `hexdump`, letting us inspect raw bytes of the file. You start looking for patterns, weird sequences, or signs of something that should be there but isn't.
- The "Concatenated Files" : Seeing weird junk in `hexdump` often means one thing, you've got multiple files or the given file might be corrupt. This was our big guess, and it's a super common CTF trick.
- Enter binwalk : It's an incredible tool for exactly this scenario. It scans a file, looking for known file "signatures" (those magic bytes we talked about) and extracts them.  Another similar tool is `foremost`.


