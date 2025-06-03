---
layout: load_md
title: The White Circle | Nahamcon 2025 | My First CTF Writeup
desc: Check out our writeup for My First CTF for Nahamcon 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: My First CTF
tags: "web, twh, rot, fuzzing"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---



> Solved by thewhiteh4t

Hint was ROT cipher, if we try to access `/flag.txt` it prints a message : `Ha, you wish!`. So the idea here was that we can use ROT cipher one `flag.txt` and one of the variants should work. To generate the wordlist of each variant I used chatgpt : 


    ROT01: gmbh.uyu
    ROT02: hnci.vzv
    ROT03: iodj.waw
    ROT04: jpek.xbx
    ROT05: kqfl.ycy
    ROT06: lrgm.zdz
    ROT07: mshn.aea
    ROT08: ntio.bfb
    ROT09: oujp.cgc
    ROT10: pvkq.dhd
    ROT11: qwlr.eie
    ROT12: rxms.fjf
    ROT13: synt.gkg
    ROT14: tzou.hlhl
    ROT15: uapv.imim
    ROT16: vbqw.jnjn
    ROT17: wcrx.koko
    ROT18: xdsy.lplp
    ROT19: yetz.mqmq
    ROT20: zfua.nrnr
    ROT21: agvb.osos
    ROT22: bhwc.ptpt
    ROT23: cixd.ququ
    ROT24: djye.rvrv
    ROT25: ekzf.swsw

Then I used the list with [FinalRecon](https://github.com/thewhiteh4t/FinalRecon), alternatively you can use tools like `gobuster` or `ffuf`, below you can see the flag file is named as `gmbh.uyu`. Accessing the page prints the flag.


![](https://i.imgur.com/AcL7Ifw.png)


**Key Learning and Takeaways**


- The Hint : The hint which was the image in this case suggested us to think laterally : what if the filename itself is ROT-encoded?
- ROT Ciphers : ROT ciphers (like ROT13) are super basic, they just shift letters around. This challenge showed how a fundamental cipher, when applied in an unexpected place (like the filename), can totally throw you off.
- ChatGPT for Wordlists : Instead of manually generating all 25 ROT variations of "flag.txt," using ChatGPT to quickly spit out that wordlist was an efficient shortcut. It's a great example of how AI can help automate tedious tasks in CTFs.


