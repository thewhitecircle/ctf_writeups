---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Relic Maps Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: forensics
challenge: Relic Maps
tags: "forensics, bobby"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Relic Maps
> Solved by: Bobbysox

The relic maps challenge started with the link to one file called relicmaps.one. This is a onedrive file and has been the focus of recent breaches because it can bypass the Mark of the Web(MOTW). I used a tool called pyone note.


https://github.com/DissectMalware/pyOneNote



![](https://i.imgur.com/T2vTz3K.png)


Now that we have extracted all the data the next thing to do was hunt down the macros that were likely used for this attack. This attacker embedded an hta file that contained vbscript. This vbscript would effectively reach out for the “payload”

initial access vector exploit chain phish>.hta>vbscript>wmi>download>cmd_exec

![](https://i.imgur.com/pOtL7U1.png)


The only valid url out of those was the windows.bat file. It was a mess, but there were three separate parts to this payload that make it “work”

The table of obfuscated strings:

![](https://i.imgur.com/scOlEQO.png)



The commands that these strings were being mapped to:

![User-uploaded image: image.png](https://paper-attachments.dropboxusercontent.com/s_FFC39C0D0A63EEB84A0808DC546A8687D9158DC27D38645F3E335CF6E40F4111_1679283123508_image.png)


This isnt actually encryption or encoding, its a long and obnoxious obfuscation technique instead.
The values pictured above, were mapped to a section of encrypted text that appeared lower in the windows.bat file:


Unknown data:

![](https://i.imgur.com/qrX7Vjc.png)


You can confirm this by decrypting a few blocks and see if it makes sense like so:

![](https://cdn.discordapp.com/attachments/974745664112189472/1087090966701285427/image.png)



to help assist in the decryption of the text, TwH assisted with a python script that could automate this:

![](https://i.imgur.com/oA1jIrW.png)


Now that the python script did most of the heavy lifting, I just separated the script by the variables and the one function that was present:


![](https://i.imgur.com/ZFcuEO0.png)


From here it was very trivial. This has been seen in the wild and written about by sans instructors here:
https://isc.sans.edu/diary/A+First+Malicious+OneNote+Document/29470
it was quite easy as it was just reverse base64 as noted here:

```
('gnirtS46esaBmorF'[-1..-16] -join '')
```

We have completely reverse engineered the cryptographic functions and have managed to obtain the full keys. There was only one data set we havent used yet. Pretty solid guess that it is our actual payload. The only thing we had to do to get the flag was follow the sans article exactly on how they decrypted the payload.

