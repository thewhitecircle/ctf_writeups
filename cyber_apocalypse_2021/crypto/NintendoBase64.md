---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Nintendo Base64 Writeup
desc: Check out our writeup for Nintendo Base64 for Cyber Apocalypse 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: crypto
challenge: Nintendo Base64
tags: "crypto, twh"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---



> Solved by : thewhiteh4t

Cyberchef recipe :

```json
    [
      { "op": "Find / Replace",
        "args": [{ "option": "Regex", "string": " " }, "", true, false, true, false] },
      { "op": "Find / Replace",
        "args": [{ "option": "Regex", "string": "\\n" }, "", true, false, true, false] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] }
    ]
```

