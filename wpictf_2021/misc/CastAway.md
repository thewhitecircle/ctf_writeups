---
layout: load_md
title: The White Circle | Wpictf 2021 | Cast_Away Writeup
desc: Check out our writeup for Cast_Away for Wpictf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: misc
challenge: Cast_Away
tags: "misc, chronocruz, game, minecraft, nbt"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---



> solved by : chronocruz.exe

* We are provided with Minecraft world data.
* Extract the files and now we need a tool called the NBT Explorer which can be used to read Minecraft’s proprietary Named Binary Tag (NBT) file format.
* Now the description says three players ran off with the flag.
* So the flag must be split between the three players.

* Opening the first file in playerdata folder we get

![](https://i.imgur.com/Z9aWAri.png)

* Further expanding and studying the tree we find 

![](https://i.imgur.com/XpP9LQB.png)

* The value of EnderItems > tag > display > Name seems to be the end part of a flag.
Note this value down.

* Repeating the process for the next playerdata file

![](https://i.imgur.com/iP4yecb.png)

* This time we find a flag like string in Inventory > tag > pages

* For the third playerdata however :
    * There was no relevant information directly visible in the data.
    * Looking at the coordinates of the player we find

![](https://i.imgur.com/6DVRJio.png)

* Using the Search > Chunk Finder function
* We input X and Z coordinates but I was unable to find the exact chunk/block values.

* Moving onto a second tool called Universal Minecraft Editor which a wonderful tool available for free at https://www.universalminecrafteditor.com/

* We load the minecraft world data into this tool and using the chunk locator input X and Z coordinates

![](https://i.imgur.com/kwTXQLK.png)

* We see there is a sign post within the chunk we located.
* Opening this chunk reveals the in-game objects within this region and their attributes

![](https://i.imgur.com/zNcf77L.png)

* We clearly see the value of Text2 looks like the beginning of our string
* Concatenating the three parts we get the full string that is our flag

```
WPI{zaQa4ttIyNo}
```