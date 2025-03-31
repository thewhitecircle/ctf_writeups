---
layout: load_md
title: The White Circle | Hsctf 2021 | message-board Writeup
desc: Check out our writeup for message-board for Hsctf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hsctf 2021
parent: hsctf_2021
category: web
challenge: message-board
tags: "web, taz"
date: 2021-06-20T00:00:00+00:00
last_modified_at: 2021-06-20T00:00:00+00:00
---


> Solved by: Taz34

I logged in using the given credentials.

Found a cookie named userData with userID and username

![](https://i.imgur.com/HZzaAdD.png)

here userID is `972` and username is `kupatergent`

Now i started looking into the given server code files and in one of the files named app.js
i found this:

![](https://i.imgur.com/SuX3MTK.png)

that indicates that we don’t need password for admin access we just need the correct user id.

So, now i fired up BurpSuite sent the request with cookie to the intruder replaced the username with admin and set the payload parameter at userID

```
Cookie: userData=j%3A%7B%22userID%22%3A%22§9§%22%2C%22username%22%3A%22admin%22%7D
```
![](https://i.imgur.com/YcqzWor.png)

Payload settings:

![](https://i.imgur.com/hvg4MID.png)

Also set up a grep match for flag{ as that is the starting of the flag

![](https://i.imgur.com/5VF3VGA.png)

And now we have to look for a ticked checkbox for flag{ and in the response section of that response we have the flag

![](https://i.imgur.com/4K3oZKR.png)

here we have the flag, we can confirm it on the website by changing the cookie values:

```
userID = 768
username = admin
```

![](https://i.imgur.com/loSsK8P.png)

```
flag{y4m_y4m_c00k13s}
```

