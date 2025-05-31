---
layout: load_md
title: The White Circle | Nahamcon 2025 | Screenshot Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: warmup
challenge: Screenshot
tags: "warmup, twh, ocr, archive"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>


## Screenshot
> Solved by thewhiteh4t

In this challenge we got a screenshot of `hexdump` of a zip file. So I used an online OCR tool to extract the text from the image.

OCR stands for Optical Character Recognition. Alternatively we can write a python script using `tesseract` or `opencv` and achieve similar results locally.

For this challenge I used [OnlineOCR.net](https://www.onlineocr.net)


![](https://i.imgur.com/rfKxlsE.png)


After that I removed index and ASCII part as we only need the hex values to generate the file and fixed some typos like `l → 1` and `O → 0`


    504b 0304 3300 0100 6300 2f02 b55a 0000
    0000 4300 0000 2700 0000 0800 0b00 666c
    6167 2e74 7874 0199 0700 0200 4145 0300
    003d 42ff d1b3 5f95 0314 24f6 8b65 c3f5
    7669 f14e 8df0 003f e240 b3ac 3364 859e
    4c2d bc3c 36f2 d4ac c403 7613 85af e4e3
    f90f bd29 d91b 614b a2c6 efde 11b7 1bcc
    907a 72ed 504b 0102 3f03 3300 0100 6300
    2f02 b55a 0000 0000 4300 0000 2700 0000
    0800 2f00 0000 0000 0000 2080 b481 0000
    0000 666c 6167 2e74 7874 0a00 2000 0000
    0000 0100 1800 8213 8543 07ca db01 0000
    0000 0000 0000 0000 0000 0000 0000 0199
    0700 0200 4145 0300 0050 4b05 0600 0000
    0001 0001 0065 0000 0074 0000 0000 00

Then using `xxd` I converted the dump back to binary and get the zip file


    xxd -r -p <input_file> <output_file>


![](https://i.imgur.com/BO82p5C.png)


**Key Learning and Takeaways**


- Recovering files : Using `xxd` is like magic, it takes that raw `hexdump` and converts it right back into a usable binary file. It's a fundamental skill for reversing and forensics, letting you reconstruct files from their hex representation.
- Know your tools : While I used an online tool for OCR, it's cool to know you could also script something locally with Python libraries like Tesseract or OpenCV. It just goes to show how many different ways there are to solve a problem


