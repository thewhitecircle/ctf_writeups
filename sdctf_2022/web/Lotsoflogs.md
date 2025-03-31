---
layout: load_md
title: The White Circle | Sdctf 2022 | Lots of logs Writeup
desc: Check out our writeup for Lots of logs for Sdctf 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Sdctf 2022
parent: sdctf_2022
category: web
challenge: Lots of logs
tags: "web, twh"
date: 2022-05-10T00:00:00+00:00
last_modified_at: 2022-05-10T00:00:00+00:00
---


> Solved By : thewhiteh4t

The blog contains links to 3 log files.
As per hints in challenge description we are supposed to find more log files and as an assumption one of them will contain the flag

On the basis of the URL of the 3 available log files I created a small custom wordlist :

```
    > cat wordlist.txt
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22
    23
    24
    25
    26
    27
    28
    29
    30
    31
    2010
    2011
    2012
    2013
    2014
    2015
    2016
    2017
    2018
    2019
    2020
    2021
    2022
    Mon.log
    Tue.log
    Wed.log
    Thu.log
    Thur.log
    Fri.log
    Sat.log
    Sun.log
```

then I used `ffuf` to fuzz the challenge site to find more logs…

```
ffuf -w wordlist.txt -u https://logs.sdc.tf/logs/FUZZ -recursion -recursion-depth 5 -o results.txt -of csv
```

in the result I gathered over 1900 logs!

![](https://i.imgur.com/SHZp6uE.png)


After this I attempted to curl them and find `sdctf` in them but it did not work so flag wasn’t available directly. Then I downloaded all available logs 😞 using wget

After wasting some hours :

```
grep -vrnw "SELL\|FIN\|PROC\|LOG\|PROF" .
```

so `-v` is used for invert grep, I used it because I was looking for unusual stuff, if you look at the log most of them have these keywords like SELL etc which were normal text

![](https://i.imgur.com/Dcap7mT.png)

so here we can see that some of the bash history is leaked

```
nc logger.sdc.tf 1338
```

and a password

```
82d192aa35a6298997e9456cb3a0b5dd92e4d6411c56af2169bed167b53f38d
```

![](https://i.imgur.com/vo6yoS6.png)
