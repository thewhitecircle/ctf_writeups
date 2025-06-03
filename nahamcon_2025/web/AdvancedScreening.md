---
layout: load_md
title: The White Circle | Nahamcon 2025 | Advanced Screening Writeup
desc: Check out our writeup for Advanced Screening for Nahamcon 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: Advanced Screening
tags: "web, twh, js, api"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---



> Solved by thewhiteh4t

We are welcomed with an employee screening form in this challenge. We had to enter an employee email to proceed, so for testing I simply sent a random email to check for response, the page itself doesn’t show any useful error response but the response of API call in browser network inspect tool shows an error message, and it leaks the valid domain the API will accept :  


![](https://i.imgur.com/7DrVRr5.png)


After adding the correct domain, the service asks for an OTP. At this point I decided to look into any available javascripts present on the page : 


    <script src="/assets/js/app.js"></script>

This script exposes few more endpoints which we can use to our advantage : 


    const tokenResponse = await fetch('/api/screen-token', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ user_id: data.user_id })
    });
    // and
    window.location.href = `/screen/?key=${tokenData.hash}`;

So we can see that `user_id` is being sent to `/api/screen-token`, this suggested that we can query it with different user IDs and see if something juicy shows, and it did on `user_id : 7` and below you can see it responded with a hash value and above you can see the script sends a hash in `key` parameter : 


![](https://i.imgur.com/bzT0hpP.png)


Sending the hash in the `key` parameter prints the flag!


![](https://i.imgur.com/NnfjShy.png)


**Key Learning and Takeaways**


- Always check requests : That's where we found the hidden error message leaking the valid domain the API would accept. Alternatively you can always use `burpsuite` which will intercept and show all these requests by default.
- JavaScript Often Hides Secrets : JavaScript files frequently expose hidden API endpoints, internal logic, and parameters that aren't immediately obvious from just looking at the page.
- API Endpoint Enumeration : Peeking into `app.js` revealed `/api/screen-token` and, crucially, that it expected a `user_id`. When we hit `user_id: 7`, it gave us a hash value. This is a classic pattern, when you find an API endpoint that takes an ID, try enumerating common IDs (like 1, 2, 3... or common ranges) to see what secrets it might expose.

