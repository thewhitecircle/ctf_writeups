---
layout: load_md
title: The White Circle | Metasploit 2021 | 4 of diamonds Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Metasploit 2021
parent: metasploit_2021
category: 4_of_diamonds
challenge: 4 of diamonds
tags: "web, starry"
date: 2021-12-08T00:00:00+00:00
last_update: 2021-12-08T00:00:00+00:00
---

<h1 class="heading card-title white-text">Metasploit 2021</h1>
## 4 of diamonds

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