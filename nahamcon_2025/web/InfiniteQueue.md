---
layout: load_md
title: The White Circle | Nahamcon 2025 | Infinite Queue Writeup
desc: Check out our writeup for Infinite Queue for Nahamcon 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: Infinite Queue
tags: "web, legend, jwt"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---


> Solved by Legend

In this challenge there is a website in which we need to buy a ticket, but we are added to a long queue.


![](https://i.imgur.com/sf9G3EA.png)


There was a JWT token to us which contains the info for our ticket.


![](https://i.imgur.com/BQEzHAg.png)


So I modified the value for `queue_time` but got an error because the token's signature was failing, but the server responded with too much information including the `queue_time`. 

![](https://i.imgur.com/pjWycz6.png)


I used the `JWT_SECRET` to sign the token which I modified and got in to purchase the ticket.

![](https://i.imgur.com/q4m9Sz2.png)


Once the purchase is done we can get the flag from the API request of the ticket PDF.


![](https://i.imgur.com/D5GR7Mr.png)


**Key Learning and Takeaways**

- Don’t trust JWTs blindly they can be modified if the secret is known or weak.
- Error messages can leak useful information always read them carefully.
- JWT signing is a common web security flaw when secrets are exposed or guessable.


