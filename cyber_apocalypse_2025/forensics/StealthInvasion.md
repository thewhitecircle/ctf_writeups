---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Stealth Invasion Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: forensics
challenge: Stealth Invasion
tags: "forensics, twh, memdump, volatility"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>
## Stealth Invasion
> Solved by thewhiteh4t


1. PID of original chrome process : `4080`

```    
    vol -f memdump.elf windows.cmdline.CmdLine
    4080 chrome.exe "C:\Program Files\Google\Chrome\Application\chrome.exe"
```

2. What is the only Folder on the Desktop : `malext`

```    
    vol -f memdump.elf windows.filescan.FileScan > file_list.txt
    0xa708c8d9ec30 \Users\selene\Desktop\malext\background.js
```

3. What is the Extention's ID : `nnjofihdjilebhiiemfmdlpbdkbjcpae`

```    
    cat file_list.txt | grep Chrome | grep Extension
    0xa708c8830c80 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\LOG
```

4. After examining the malicious extention's code, what is the log filename in which the datais stored : `000003.log`

```    
    cat file_list.txt| grep Chrome | grep nnjofihdjilebhiiemfmdlpbdkbjcpae
    0xa708c8830c80 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\LOG
    0xa708c8dd5be0 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\MANIFEST-000001
    0xa708c8dda230 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\CURRENTdbtmp
    0xa708c8f2b500 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae
    0xa708c8f2d760 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae
    0xa708cab9a2c0 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\LOG
    0xa708caba14d0 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\000003.log
```

5. What is the URL the user navigated to : `drive.google.com`

```
    vol -f memdump.elf windows.dumpfiles.DumpFiles --virtaddr 0xa708caba14d0
    cat file.0xa708caba14d0.0xa708c9d90d00.DataSectionObject.000003.log.dat
    log"drive.google.comEnter\r\nsel"�d�log"drive.google.comEnter\r\nsele" w
    3log!"drive.google.comEnter\r\nselene"|:log("drive.google.comEnter\r\nselene|Shift|"Xu@<;log)"drive.google.comEnter\r\nselene|Shift|@"�g<log*"drive.google.co
```

6. What is the password of selene@rangers.eldoria.com : `clip-mummify-proofs`

in the same log file :

```
    drive.google.comEnter\r\nselene|Shift|@rangers.eldoria.comEnter\r\nclip-mummify-proofs"8jsAloga"drive.google.comEnter\r\nselene|Shift|@rangers.eldoria.comEnter\r\nclip-mummify-proofsEnter\r\n
```

