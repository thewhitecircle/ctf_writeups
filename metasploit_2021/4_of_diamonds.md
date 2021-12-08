# 4 of diamonds

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