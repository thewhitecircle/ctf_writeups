---
layout: load_md
title: The White Circle | Wpictf 2021 | Pokemon Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: misc
challenge: Pokemon
tags: "misc, chronocruz, game, gameboy, emu"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---

<h1 class="heading card-title white-text">Wpictf 2021</h1>

## Pokemon

> solved by : chronocruz.exe

* Using gbgw64 Gameboy emulator
* Load up the provided pokemon ROM
* Start the game
* Continue to tall grasses towards north after exiting your house
* Professor Oak will stop you saying its dangerous to go without a pokemon
* Then he will take you to his lab and give a choice between three pokeballs
* Left most = Charmander
* Middle = Squirtle
* Right most = Bulbasaur
* (Not that these choices matter anyway)
* Soon as you pick your pokemon your rival will choose his pokeball and no matter which one he choses he will get a Mewtwo, which is basically the strongest pokemon in this game. And it is level 50 to further salt your wounds.

* Simple way to win, find the cheat menu within your gameboy emulator
* (In gbgw64 you just right click and there it is)
* Add the following codes
* 01FF16D0 - Unlimited HP so you don’t die
* 0170E9CF - Enemy can’t attack and is hurt every turn with a burn status effect
* Patiently keep attacking your enemy till his health drops to zero and you get the following screen.

![](https://i.imgur.com/PxVuCDc.jpg)

* Congratulations, you’re the best pokemon trainer (not really).

