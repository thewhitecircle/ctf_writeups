---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Extraterrestrial persistence Writeup
desc: Check out our writeup for Extraterrestrial persistence for Cyber Apocalypse 2023 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: forensics
challenge: Extraterrestrial persistence
tags: "forensics, twh"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---



> Solved by : thewhiteh4t


- A bash script is given which installs a systemd service
- one of the lines contains an `echo` command :

```
echo -e "W1VuaXRdCkRlc2NyaXB0aW9uPUhUQnt0aDNzM180bDEzblNfNHIzX3MwMDAwMF9iNHMxY30KQWZ0ZXI9bmV0d29yay50YXJnZXQgbmV0d29yay1vbmxpbmUudGFyZ2V0CgpbU2VydmljZV0KVHlwZT1vbmVzaG90ClJlbWFpbkFmdGVyRXhpdD15ZXMKCkV4ZWNTdGFydD0vdXNyL2xvY2FsL2Jpbi9zZXJ2aWNlCkV4ZWNTdG9wPS91c3IvbG9jYWwvYmluL3NlcnZpY2UKCltJbnN0YWxsXQpXYW50ZWRCeT1tdWx0aS11c2VyLnRhcmdldA=="|base64 --decode > /usr/lib/systemd/system/service.service
```

- Decoding the base64 string gives us the flag

```
HTB{th3s3_4l13nS_4r3_s00000_b4s1c}
```

