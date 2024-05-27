---
layout: load_md
title: The White Circle | Sdctf 2021 | Git Good Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2021
parent: sdctf_2021
category: web
challenge: Git Good
tags: "web, twh, git"
---

<h1 class="heading card-title white-text">Sdctf 2021</h1>

## Git Good
> Solved By : thewhiteh4t

* Robots reveal an `admin` page and `.git` directory

![](https://i.imgur.com/vORc05a.png)

* I used a very useful tool which helps to dump file contents of websites which use GIT version control system
* https://github.com/internetwache/GitTools

* `.git` directory dumped :

![](https://i.imgur.com/zfQ1jga.png)

* now we can use extractor to get files from the dump

![](https://i.imgur.com/0gRaxDM.png)

* I found 2 `users.db` files, both are sqlite3 databases
* one contains `bcrypt` hashes and other one contains `md5` hashes
* I cracked md5 hashes first because they take less time

![](https://i.imgur.com/RRJlEIX.png)

* one valid credential found :

```
aaron@cgau.sdc.tf : weakpassword
```

* login on `admin.html` and we get the flag

![](https://i.imgur.com/VIC6bxW.png)