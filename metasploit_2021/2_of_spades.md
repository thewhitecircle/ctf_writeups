# 2 of spades

> Solved by: Taz34

Did a NIKTO scan on the target

```
nikto -h http://172.17.15.117:443/
```

and one of the lines in the result showed this :

```
+ /.env: .env file found. The .env file may contain credentials.
```

so i headed to this sub directory

![](https://i.imgur.com/OpsJm7G.png)

inserted the file name in the URL

```
http://172.17.15.117:443/3e6f0e21-7faa-429f-8a1d-3f715a520da4.png
```

![](https://i.imgur.com/BvCCwrW.png)

and we have the flag.