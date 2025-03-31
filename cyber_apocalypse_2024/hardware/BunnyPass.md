---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | BunnyPass Writeup
desc: Check out our writeup for BunnyPass for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: hardware
challenge: BunnyPass
tags: "hardware, starry, rabbitmq"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by : Starry-lord

In this challenge we only have a docker instance, here is what we see: 

![login rabbitmq](https://i.imgur.com/4uAi61N.png)

> **RabbitMQ** is a reliable and mature messaging and streaming broker
> -- [https://www.rabbitmq.com](https://www.rabbitmq.com/)

When we research about RabbitMQ online we can find interesting details, such as the the default password for an installation which is :
`guest:guest` .


![dashboard rabbitmq](https://i.imgur.com/VTip8Yo.png)


We can see an admin panel, which allows to edit user rights and access to any ressource, and also shows the guest user has the administrator role, for some reason. 


![queues rabbitmq](https://i.imgur.com/ZAuMwYd.png)


We then notice a few Messages are ready so we can click to see whats going on in `quality_control`, `production_logs`, `maintenance_logs`, `factory_idle`, etc. The flag is in `factory_idle`.


![rabbitmq get messages](https://i.imgur.com/NOlwfx0.png)

![flag in messages](https://i.imgur.com/h4ZZpj9.png)


```
HTB{th3_hunt3d_b3c0m3s_th3_hunt3r}
```

