---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Gunhead Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: web
challenge: Gunhead
tags: "web, legend, rce"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Gunhead 
> Solved by Legend

Challenge description


> During Pandora's training, the Gunhead AI combat robot had been tampered with and was now malfunctioning, causing it to become uncontrollable. With the situation escalating rapidly, Pandora used her hacking skills to infiltrate the managing system of Gunhead and urgently needs to take it down.

We are provided with a URL and docker file for the challenge. 

The website running showing the status report of the combat robot along with a command prompt to run some commands.

![](https://i.imgur.com/ALqLFrv.png)

![](https://i.imgur.com/ZoeReHi.png)


Going through the docker file I found a hint.


![](https://i.imgur.com/eHZdlRv.png)


Here `shell_exec` is running which runs a command in a shell and returns the result of the output. And the hint suggested that it’s not sanitized so we can try to escape it and run shell commands.


![](https://i.imgur.com/mg4ZrUo.png)


It worked. So now we can simply read the flag.

![](https://i.imgur.com/EQ0w8ui.png)

