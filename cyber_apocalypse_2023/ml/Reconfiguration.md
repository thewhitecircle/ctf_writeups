---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Reconfiguration Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: ml
challenge: Reconfiguration
tags: "ml, bobby"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Reconfiguration
> Solved by : Bobbysox

Challenge description


> Reconfiguration
> As Pandora set out on her quest to find the ancient alien relic, she knew that the journey would be treacherous. The desert was vast and unforgiving, and the harsh conditions would put her cyborg body to the test. Pandora started by collecting data about the temperature and humidity levels in the desert. She used a scatter plot in an Orange Workspace file to visualize this data and identified the areas where the temperature was highest and the humidity was lowest. Using this information, she reconfigured her sensors to better withstand the extreme heat and conserve water. But, a second look at the data revealed something otherwordly, it seems that the relic's presence beneath the surface has scarred the land in a very peculiar way, can you see it?

We are provided with two files for this. An OWS file, and a .CSV file. After some googling I found out that .ows files can be run in a program called “Orange”


![](https://i.imgur.com/J23sbiB.png)


From here it was honestly just a matter of toying with it. The csv file is a data table, so I chose the data table option and linked it to the scatter plot. This ultimately revealed the flag.

![](https://i.imgur.com/OOVgwTm.png)


