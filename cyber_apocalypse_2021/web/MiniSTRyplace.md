---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | MiniSTRyplace Writeup
desc: Check out our writeup for MiniSTRyplace for Cyber Apocalypse 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: web
challenge: MiniSTRyplace
tags: "web, bobby, twh, lfi"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---



> Solved by: Bobbysox and thewhiteh4t

* hint1: “Let's read this website in the language of Alines. Or maybe not?
This challenge will raise 33 euros for a good cause.”
* hint2: The challenges name is “miniSTRyplace”. This is a play on words for str_replace.
* We see right away that we can change language and it is represented:
`ip.address/?lang=es.php` a perfect place to try LFI

* In order to prevent path traversal, developers can implement blacklisting. It will usually look something like this:

```
$language = str_replace('../', '', $_GET['language']);
```

* This is where you can see the name of the challenge was a hint to the sec measures used.
*now we can modify our final payload to bypass blacklisting:

```
http://46.101.77.180:32490/?lang=....//....//....//....//....//....//....//....//etc/passwd
```

* Our exploit is successful, to get the flag we just used the following :

```
http://165.227.234.7:30779/?lang=....//....//flag
```

