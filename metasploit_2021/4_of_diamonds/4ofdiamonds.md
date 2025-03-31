---
layout: load_md
title: The White Circle | Metasploit 2021 | 4 of diamonds Writeup
desc: Check out our writeup for 4 of diamonds for Metasploit 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: 4_of_diamonds
challenge: 4 of diamonds
tags: "web, starry"
date: 2021-12-08T00:00:00+00:00
last_modified_at: 2021-12-08T00:00:00+00:00
---


> Solved by: Starry-Lord

```
Port 10010
```

Here we had a web app with a register and login page.

Registering gave access to the website and showed that the user details were stored in a javascript variable in the response.

```javascript
<script>
  var current_account = {
    "id":2,"username":starlord,"role":"user","created_at":
    "2021-12-03T6:36:42.821Z","updated_at":"2021-12-03T6:36:42.821Z"
  };
</script>
```

By intercepting the register request, I noticed I was sending account[username]=starlord like parameters, and decided to add account[role]=admin to see if it would give me admin privilege.

By doing so I got a session with an additional admin button, which gave me the flag.

![](https://i.imgur.com/dwZKwNy.png)