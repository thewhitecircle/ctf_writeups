---
layout: load_md
title: The White Circle | Gpnctf 2024 | todo Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Gpnctf 2024
parent: gpnctf_2024
category: web
challenge: todo
tags: "web, twh, xss, csp bypass"
---

<h1 class="heading card-title white-text">Gpnctf 2024</h1>


## todo
> Solved by : thewhiteh4t


- when we input html into admin form input then the following flow is executed in back-end :

```
/admin -> set flag cookie -> visit site -> fill our input in /chal input -> screenshot after delay
```

- flow in `/chal` route :

```
CSP is set -> script.js is added to head tag -> our input is added
```

- flow in `/script.js` route :

```
if the flag cookie matches -> fake flag is replaced with actual flag -> response is sent
```

- so the actual flag is stored inside `script.js`
- CSP :

```
default-src 'none'; script-src 'self' 'unsafe-inline';
```

- cannot load js url like `/script.js` in src because connect-src is not present so default-src fallback is used which is none so request is blocked
- thanks to unsafe-inline we can execute js scripts
- after looking into default-src bypass I found the following on hacktricks :


![](https://i.imgur.com/J1hLfFK.png)

- [Hacktricks CSP Bypass](https://book.hacktricks.xyz/pentesting-web/content-security-policy-csp-bypass)
- so I tried to load `/script.js` in form action like this :

```html
<form action="/script.js" id="plz">
    <button type="submit"></button>
</form>
<script>document.getElementById("plz").submit();</script>
```

![](https://i.imgur.com/uQD36xS.png)


