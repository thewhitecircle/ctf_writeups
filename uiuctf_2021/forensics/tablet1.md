---
layout: load_md
title: The White Circle | Uiuctf 2021 | tablet 1 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Uiuctf 2021
parent: uiuctf_2021
category: forensics
challenge: tablet 1
tags: "forensics, twh"
---

<h1 class="heading card-title white-text">Uiuctf 2021</h1>

## tablet 1
> Solved by : thewhiteh4t

- we are given an iPad image in this challenge
- goal was to find a server where the data was exfiltrated
- so a guess was that I will find a server and flag will be present on it
- following this theory I looked for the string `uiuc.tf` because thats the domain being used in all remote based challenges


    grep -rni "uiuc.tf" .


![](https://i.imgur.com/2AoBT9W.png)

- this is a Sqlite3 database file
- I found SSH private key and other login info required for login


![](https://i.imgur.com/dkItbjg.png)



- SSH login
    ssh -i id_rsa red@red.chal.uiuc.tf -p 42069


- the private key is encrypted and the password is actually :
    ********


- but on login I got a message that service allows only SFTP connections, so I tried logging in with SFTP


![](https://i.imgur.com/nzKD3JV.png)

- unlike most ctf challenges flag was not available after login so I downloaded `.bash_history` using `get` command


![](https://i.imgur.com/DXCDagb.png)

- and in this location I found a JPG image…


![](https://i.imgur.com/VgitoW9.png)



![](https://i.imgur.com/rN1zUxn.png)



    uiuctf{upload_task_only_takes_9_seconds_0bf79b}


