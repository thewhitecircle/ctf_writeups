---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | 2EZ Writeup
desc: Check out our writeup for 2EZ for Hacktivitycon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: warmups
challenge: 2EZ
tags: "warmup, twh, magic bytes"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---




> Solved By : thewhiteh4t


- We are given a file named `2ez`
- the file format is not known when I tested it with `file` command


![](https://i.imgur.com/v39kj3k.png)

- next I tried `binwalk` to look for any hidden files but the output was blank
- next I checked the MAGIC of the file i.e. the header
- file magic is responsible for the correct file format
- file command checks magic and file footer to determine correct file type


![](https://i.imgur.com/SmobPBK.png)



- JFIF header means a jpeg file
- correct header for JFIF in hex is : `FF D8 FF`
- but if we look at the file given to us its different, so I fixed it using a hex editor


![](https://i.imgur.com/vPgfam0.png)

- Saved it as a new file and solved


![](https://i.imgur.com/rIBarpI.png)

