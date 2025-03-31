---
layout: load_md
title: The White Circle | Nahamcon 2024 | Breath of the wild Writeup
desc: Check out our writeup for Breath of the wild for Nahamcon 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: forensics
challenge: Breath of the wild
tags: "forensics, twh, vhdx, bitlocker, autopsy, "
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---


> Solved by : thewhiteh4t


- We are given a file without extension, running file command on it shows that its a VHDX file :

```
> file breath-of-the-wild
breath-of-the-wild: Microsoft Disk Image eXtended, by Microsoft Windows 10.0.22631.0, sequence 0xa; LOG; region, 2 entries, id BAT, at 0x300000, Required 1, id Metadata, at 0x200000, Required 1
```

- fastest way to access the disk file is by mounting it in windows, it is bitlocker enabled and we are given a password : `videogames`
- after unlock we can see about 100 wallpapers, the challenge description hints at finding website from where these are downloaded


![](https://i.imgur.com/ntLt6eT.png)



- using `Autopsy` we can automatically get a list of web locations for each file in a matter of seconds : 


![](https://i.imgur.com/OzxcGPt.png)



- one of the URL is using a different domain and it has some extra data which can be decoded in CyberChef : 


![](https://i.imgur.com/1PvaIro.png)



