---
layout: load_md
title: The White Circle | Nahamcon 2021 | hercules Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: mission
challenge: hercules
tags: "osint"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## hercules

flag is in “connect.sh” in his repo : https://github.com/HerculesScox/maintenance/blob/main/connect.sh

along with the flag we can also see he that he used “sshpass” in this file, sshpass is a program used to auto login into ssh by supplying the password in command line instead of manually entering it everytime

```
hercules:starstruckherc
```

with these credentials we can SSH into DEGRADE challenge!

