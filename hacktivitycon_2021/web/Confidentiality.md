---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Confidentiality Writeup
desc: Check out our writeup for Confidentiality for Hacktivitycon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: web
challenge: Confidentiality
tags: "web, taz"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---



> Solved by: Taz34


- here we have a service which lists all the items in the mentioned dir
- so i simply started looking for flag, look for elements in the /home dir
- here we have a user dir and in that we have the flag.txt
![](https://i.imgur.com/6I1hCb4.png)


now to red the file

    /home/user & cat /home/user/flag.txt
![](https://i.imgur.com/G0AlZ6s.png)


here we have the flag