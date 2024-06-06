---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Roten Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: forensics
challenge: Roten
tags: "forensics, twh, pcap"
date: 2023-03-27T00:00:00+00:00
last_update: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>


## Roten
> Solved by : thewhiteh4t


- A PCAP file is given again
- Apply a filter to view only POST requests :

```
http.request.method=="POST"
```

- In one of the requests we can see a PHP file by the name `galacticmap.php` is uploaded


![](https://i.imgur.com/MCqcnwa.png)

- It is a obfuscated PHP file, in its last line of code `eval` function is executed
- To de-obfuscate I commented out the eval and added an `echo`


![](https://i.imgur.com/Y607MYg.png)

- After running this PHP file we can get the flag :


![](https://i.imgur.com/B4RO5aI.png)

