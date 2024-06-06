---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Artifacts of disastrous sightings Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: forensics
challenge: Artifacts of disastrous sightings
tags: "forensics, twh, vhdx, powershell, obfuscation"
date: 2023-03-27T00:00:00+00:00
last_update: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>


## Artifacts of disastrous sightings
> Solved by : thewhiteh4t


- In this challenge we start with a `vhdx` file which is a type of virtual hard disk

```
> file 2023-03-09T132449_PANDORA.vhdx
2023-03-09T132449_PANDORA.vhdx: Microsoft Disk Image eXtended, by .NET DiscUtils, sequence 0x8; LOG; region, 2 entries, id Metadata, at 0x200000, Required 1, id BAT, at 0x300000, Required 1
```

- Before we can mount this disk we need to find partition information using `guestfish` :

```
> guestfish -a 2023-03-09T132449_PANDORA.vhdx

Welcome to guestfish, the guest filesystem shell for
editing virtual machine filesystems and disk images.

Type: ‘help’ for help on commands
      ‘man’ to read the manual
      ‘quit’ to quit the shell

><fs> run
><fs> list-filesystems
/dev/sda1: ntfs
><fs> exit
```

- Now we can mount it using `guestmount` :

```
> guestmount -a 2023-03-09T132449_PANDORA.vhdx -m /dev/sda1 --ro mpoint
```

- Now I started enumerating with the given hints in challenge description :

```
> notices the Windows Event Viewer tab open on the Security log
> takes a snapshot of her machine and shuts it down
> diving deep down and following all traces
```

- After some digging I landed on powershell command history :

```
C/Users/Pandora/AppData/Roaming/Microsoft/Windows/PowerShell/PSReadline
```

![](https://i.imgur.com/YrLRwlP.png)

- So attackers executed a command and removed powershell logs
- At this point I went looking into multiple ways of extracting and reading powershell EVTX logs but could not find anything useful
- Then I finally realized that the command is actually creating a `hidden.ps1` in `Alternate Data Stream` , this is an NTFS specific feature and attackers can hide payloads in ADS
- After this I realized the title of the challenge is also a big hint which I totally missed otherwise it would have saved lot of time
- After switching to windows I confirmed my doubts and it was indeed ADS :

```
PS E:\C\Windows\Tasks> Get-Item * -Stream *
```

![](https://i.imgur.com/snazniP.png)

- for reading `hidden.ps1` :

```
PS E:\C\Windows\Tasks> get-item .\ActiveSyncProvider.dll | Get-Content -Stream 'hidden.ps1'
```

![](https://i.imgur.com/2QDH47H.png)

- I got excited and quickly loaded the encoded string in cyberchef and this happened :


![](https://i.imgur.com/6nOu6pe.png)

- A very long string of these special characters, at this point I kept trying to fix the base64 because there are many sections which are duplicate
- Finally I gave up on fixing and started to find more about this type of encoding and after sometime landed on this project :


https://github.com/danielbohannon/Invoke-Obfuscation

- This project uses a technique found by 牟田口大介 (@mutaguchi) :


http://perl-users.jp/articles/advent-calendar/2010/sym/11

- This turned out to be a custom encoding technique and I could not find any de-obfuscator for it, so the easiest way forward was to just run the script and extract the script blocks from EVTX files in windows
- By default module logging and script block logging are disabled, I enabled both in GPO


![](https://i.imgur.com/4to0yFu.png)

- After this I executed `hidden.ps1` and checked event logs and finally I saw some readable code :


![](https://i.imgur.com/1tfba2L.png)

- Now I started looking for more code but not much was visible in viewer so I started looking for something to extract script blocks and found this nice blog :


https://vikas891.medium.com/join-powershell-script-from-event-logs-12deef6dd5ab


https://gist.githubusercontent.com/vikas891/841ac223e69913b49dc2aa9cc8663e34/raw/ce73035acd161c181da2bc9bb4fdab235e0f0de2/ExtractAllScripts.ps1


- The script provided at the end worked perfectly!


![](https://i.imgur.com/ukpDLfC.png)

- In one of the scripts I got the flag :


![](https://i.imgur.com/yRW3OAP.png)

