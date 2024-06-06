---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Packet cyclone Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: forensics
challenge: Packet cyclone
tags: "forensics, twh, evtx, chainsaw"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>


## Packet cyclone
> Solved by : thewhiteh4t


- We are given Windows EVTX files and sigma rules for detecting exfiltration using `rclone`
- To scan these EVTX files we can use `chainsaw` which supports sigma rules

```
chainsaw hunt -s sigma_rules -m sigma-event-logs-all.yml Logs
```

- Two detection are shown
- First one contains credentials of `mega.nz`
![](https://i.imgur.com/wh4wNy1.png)

- Second contains file paths


![](https://i.imgur.com/IooHAxJ.png)

- Here are the correct answers based on information given in these two detection :


![](https://i.imgur.com/qajxXjN.png)

