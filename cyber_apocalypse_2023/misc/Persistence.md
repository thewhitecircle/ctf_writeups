---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Persistence Writeup
desc: Check out our writeup for Persistence for Cyber Apocalypse 2023 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: misc
challenge: Persistence
tags: "misc, legend, scripting"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---


> Solved by Legend

Challenge description


> Thousands of years ago, sending a GET request to /flag would grant immense power and wisdom. Now it's broken and usually returns random data, but keep trying, and you might get lucky... Legends say it works once every 1000 tries.

In this challenge we are given `IP` along with `Port Number`  to connect to the challenge and retrieve the flag using the `GET` request.

Initially checking the URL it gave something gibberish.

![](https://i.imgur.com/Yf7EiWW.png)


The hint of the challenge was that the `/flag` works once in 1000 tries. So I made a loop to do the same.


    for i in `seq 1 1000`; do curl -s http://68.183.45.143:31981/flag | grep -i "HTB" ; done

And after running for sometime it gave the flag.

![](https://i.imgur.com/WbpHGPu.png)

