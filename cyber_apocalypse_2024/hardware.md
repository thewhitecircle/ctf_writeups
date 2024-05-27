# hardware

## BunnyPass
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

----------

## Maze
> Solved by : thewhiteh4t

- Just explore challenge files

![](https://i.imgur.com/0lcZGGJ.png)

- Read Factory.pdf

![](https://i.imgur.com/4xU2Z7N.png)