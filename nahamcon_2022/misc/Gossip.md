---
layout: load_md
title: The White Circle | Nahamcon 2022 | Gossip Writeup
desc: Check out our writeup for Gossip for Nahamcon 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2022
parent: nahamcon_2022
category: misc
challenge: Gossip
tags: "misc, starry, taz, legend, twh, cve, lpe, privesc"
date: 2022-05-03T00:00:00+00:00
last_modified_at: 2022-05-03T00:00:00+00:00
---


> Solved By : Starry-Lord, Taz, Legend, thewhiteh4t

This one involved an ssh connection to a Linux machine. We could find kubernetes secrets in the usual /run/secrets/kubernetes.io/serviceaccount which revealed it was a kubernetes container.
After a bit of enumeration I learned about CVE-2022-0185 which allows us to escape the container, but it looked like it had been patched.

Further enumeration led me to find a few sticky bits on some of the binaries in /usr/bin. I looked them up to find a fitting one called dialog, which will allow us to read files with elevated permissions:

```
user@gossip-9d9e950dfdcbda12-64cdd78676-psbqk:/usr/bin$ ls -la dialog 
-rwsr-sr-x 1 root root 260736 Jan  3 23:30 dialog
```

![](https://i.imgur.com/EZjKAA0.png)

After a bit of cleaning up we can use this key to login as root.


![](https://i.imgur.com/jdEnsST.png)

