---
layout: load_md
title: The White Circle | Nahamcon 2024 | Helpful Desk Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: Helpful Desk
tags: "web, twh, dotnet, ilspy"
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>

## Helpful Desk
> Solved by : thewhiteh4t


- In this challenge security bulletin shows 3 versions of the application
- v1.2 shows critical status
- After downloading both updates we can use `diff` to check which files were updated

```
diff -bur v1_1 v1_2
```

![](https://i.imgur.com/BBO1lGj.png)

- Using Avalonia ILSpy we can decompile the DLL file and inspect different functions
- `SetupController` contains some interesting code and it shows a URL path


![](https://i.imgur.com/UmO3325.png)

- On visiting this page we can actually set admin credentials!


![](https://i.imgur.com/Tn0ZvPH.png)

- After setting credentials we can login and inspect services to get the flag


![](https://i.imgur.com/cW9RrF3.png)

