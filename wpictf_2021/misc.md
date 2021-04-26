# misc

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

---

## Cast_Away

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