# 9 of diamonds

> Solved By : thewhiteh4t

```
Port : 8080
```

In this challenge we were presented with a website about cookies, obvious hint.
There were 3 sections :

- User Registration
- User Login
- Admin Login

First I registered a user account and checked the cookies, there were 4 cookies for our user :

- admin : false
- authenticated-user : true
- made-an-account : true
- visited-main-page : true

I just had to modify admin from false to true and send the request and we got the flag.

![](https://i.imgur.com/RxycAEW.png)