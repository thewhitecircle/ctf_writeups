---
layout: load_md
title: The White Circle | Nahamcon 2021 | Typewriter Writeup
desc: Check out our writeup for Typewriter for Nahamcon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: forensics
challenge: Typewriter
tags: "forensics, memory, volatility"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---



* first we need to find the profile for this memory dump

```
volatility imageinfo -f image.bin
```

profile is `Win7SP1x86_23418`

* now the challenge states that the files were lost in a system crash, so we need to look for files

```
volatility filescan -f image.bin --profile Win7SP1x86_23418
```

* this provides a long list of files, but we know in windows people work in desktop most of the time so after inspecting desktop path we can see this file path

```
0x000000007e841f80      8      0 RW-r-- \Device\HarddiskVolume1\Users\IEUser\Desktop\CONFIDENTIAL DOCUMENT.docx
```

* now we can try and dump this file

```
volatility -f image.bin --profile Win7SP1x86_23418 dumpfiles -Q 0x000000007e841f80 --dump-dir .
```

* using file command we can see the file format and rename appropriately but the file is corrupt, remember system crash?

* but docx files can be extracted like a zip with `unzip` command

* after extracting we get these files/dirs

```
drwxr-xr-x 5 twh users 4096 Mar 14 13:54  .
drwxr-xr-x 3 twh users 4096 Mar 14 13:54  ..
-rw-r--r-- 1 twh users 1422 Jan  1  1980 '[Content_Types].xml'
drwxr-xr-x 2 twh users 4096 Mar 14 13:53  docProps
drwxr-xr-x 2 twh users 4096 Mar 14 13:53  _rels
drwxr-xr-x 4 twh users 4096 Mar 14 13:53  word
```

* inside word we have a file named `document.xml`

* open this file in browser and flag is in plain text :D

