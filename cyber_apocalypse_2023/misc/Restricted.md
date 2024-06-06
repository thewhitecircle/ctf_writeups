---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Restricted Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: misc
challenge: Restricted
tags: "misc, legend, jail, escape, rbash"
date: 2023-03-27T00:00:00+00:00
last_update: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Restricted
> Solved by Legend

Challenge description


> You 're still trying to collect information for your research on the alien relic. Scientists contained the memories of ancient egyptian mummies into small chips, where they could store and replay them at will. Many of these mummies were part of the battle against the aliens and you suspect their memories may reveal hints to the location of the relic and the underground vessels. You managed to get your hands on one of these chips but after you connected to it, any attempt to access its internal data proved futile. The software containing all these memories seems to be running on a restricted environment which limits your access. Can you find a way to escape the restricted environment ?

In this challenge we are given SSH shell escape to get the flag.

Going through the docker file we can get some info of what configuration is done to the challenge

1. The shell is set to `rbash` which basically means restricted bash.


    RUN chown -R restricted:restricted /home/restricted


1. The flag file will have some random value appended to it as written in the config


    RUN mv /flag.txt /flag_`cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 5 | head -n 1`

Since it is a SSH escape challenge I remembered that I have solved a challenge like this before so first thing I did was to check if we can execute remote code without opening the SSH shell.

![](https://i.imgur.com/KAPfFSz.png)


It worked!! So now all I had to do was look for the flag file name as it gets changed and cat it. 

![](https://i.imgur.com/6Tce4Cu.png)


We got the flag.

![](https://i.imgur.com/MQyffnB.png)

