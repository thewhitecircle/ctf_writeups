---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | The Poisoned Scroll Writeup
desc: Check out our writeup for The Poisoned Scroll for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: osint
challenge: The Poisoned Scroll
tags: "osint, starry, guess, search"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---



> Solved by StarryLord


    CHALLENGE NAME
    The Poisoned Scroll
    In her crystal-lit sanctum, Nyla examines reports of a series of magical attacks against the ruling council of Germinia, Eldoria's eastern ally. The attacks all bear the signature of the Shadow Ravens, a notorious cabal of dark mages known for their espionage across the realms. Her fingers trace connections between affected scrolls and contaminated artifacts, seeking the specific enchantment weapon deployed against the Germinian leaders. The runes along her sleeves pulse rhythmically as she sifts through intercepted messages and magical residue analyses from the attack sites. Her network of information crystals glows brighter as patterns emerge in the magical attacks—each victim touched by the same corrupting spell, though disguised under different manifestations. Finally, the name of the specific dark enchantment materializes in glowing script above her central crystal. Another dangerous threat identified by Eldoria's master information seeker, who knows that even the most sophisticated magical weapons leave distinctive traces for those who know how to read the patterns of corruption.
    Poisoned Scroll: HTB{MalwareName}
    Example: HTB{DarkPhantom} No special characters

After decoding the Eldorian:
Basically we had to find the name of the malware that targeted German politicians:


https://cloud.google.com/blog/topics/threat-intelligence/apt29-wineloader-german-political-parties

    HTB{WineLoader}


