---
layout: load_md
title: The White Circle | Nahamcon 2021 | hercules Writeup
desc: Check out our writeup for hercules for Nahamcon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: mission
challenge: hercules
tags: "osint"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---



flag is in “connect.sh” in his repo : https://github.com/HerculesScox/maintenance/blob/main/connect.sh

along with the flag we can also see he that he used “sshpass” in this file, sshpass is a program used to auto login into ssh by supplying the password in command line instead of manually entering it everytime

```
hercules:starstruckherc
```

with these credentials we can SSH into DEGRADE challenge!

