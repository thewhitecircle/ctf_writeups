---
layout: load_md
title: The White Circle | Cyber Apocalypse 2022 | Intergalactic Recovery Writeup
desc: Check out our writeup for Intergalactic Recovery for Cyber Apocalypse 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2022
parent: cyber_apocalypse_2022
category: forensics
challenge: Intergalactic Recovery
tags: "forensics, taz, twh, starry, bobby, raid"
date: 2022-05-21T00:00:00+00:00
last_modified_at: 2022-05-21T00:00:00+00:00
---


> Solved too late by: Taz34, thewhiteh4t, Starry-Lord, Bobbysox

challenge description:

```
Miyuki's team stores all the evidence from important cases in a shared RAID 5 disk. Especially now that the case IMW-1337 is almost completed, evidences and clues are needed more than ever. Unfortunately for the team, an electromagnetic pulse caused by Draeger's EMP cannon has partially destroyed the disk. 
Can you help her and the rest of team recover the content of the failed disk? 
Download: http://134.209.177.115/forensics/forensics_intergalactic_recovery.zip
```

We get 3 files namely `disk1.img`, `disk2.img`, `disk3.img`, with absolutely no clues from the file command. After a while, and thanks to a not recently updated binwalk version we managed to find out it was ext4 files inside. It was not very useful to extract anything from it in that state and we even thought we couldn’t move forward without reconstructing the super block at some point, but we were sure the mdadm command was involved. At some point, we realized that those were partition files, thanks to the old version of autopsy with the cocker which was another very useful clue. 

It’s important to note that disk1 and 2 are 5242880 bytes large when disk 3 is only around 3000 bytes which indicates (huge) loss of data, so we have to consider it missing and focus only on disks 1 and 2.

Here is how to proceed with all this information in mind assuming we correctly guessed the correct Raid disks order after a couple tries:

```
sudo losetup -fP disk1.img
sudo losetup -fP disk2.img
sudo mdadm --create /dev/md0 --level=5 --raid-devices=3 --assume-clean /dev/loop2 missing /dev/loop1
```

option 1:

```
-f, --find [file]
        Find the first unused loop device. If a file argument is
        present, use the found device as loop device. Otherwise, just
        print its name.
```

option 2:

```
-P, --partscan
        Force the kernel to scan the partition table on a newly
        created loop device. Note that the partition table parsing
        depends on sector sizes. The default is sector size is 512
        bytes, otherwise you need to use the option --sector-size
        together with --partscan.
```

Then we can simply create a directory to mount the md0 to and browse it, let’s call it “raid”:

![](https://i.imgur.com/7ZUZKIh.png)

And here is the content of the reconstructed pdf file!!+1+!!!!1111!+1!

![](https://i.imgur.com/IF7drBw.png)

> Note from Starry-Lord: 
> If only I had tried  --assume-clean …

