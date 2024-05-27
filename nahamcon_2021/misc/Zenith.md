---
layout: load_md
title: The White Circle | Nahamcon 2021 | Zenith Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: misc
challenge: Zenith
tags: "misc, privesc, lpe, zenity"
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## Zenith

* Goal was to privesc to root and get the flag from root directory

* detecting privesc was easy because all i had to do was `sudo -l`

* we can execute `zenity` with sudo without password

* zenity is an application which generates GUI pop ups.

* but we have ssh, a simple workaround for this is to use `-X` commandline option of ssh which forwards gui applications to our machine, so using this we can execute zenity on remote server and popups will appear on our machine.

* now after reading manpage of zenity few times this command worked

```
zenity --text-info --filename "/root/.ssh/id_rsa"
```

![](https://i.imgur.com/M3C9zEb.png)

* and a pop up appears with private key of root <3

![](https://i.imgur.com/kse1aWc.png)
