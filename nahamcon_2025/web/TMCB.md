---
layout: load_md
title: The White Circle | Nahamcon 2025 | TMCB Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: TMCB
tags: "web, legend, js"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>


## TMCB
> Solved by Legend

In this challenge there are two million checkboxes that we need to check to get the flag.

![](https://i.imgur.com/BuowCay.png)


The website is using web socket's to send the checked boxes and update it on server. I tired bash script to send 300000 at a time in burp but at around 1700000 the connection got reset, so I had to make a script.

I tried using JavaScript to send the checked boxes in batch and it worked.


    const ws = new WebSocket('ws://' + location.host + '/ws');
    ws.onopen = () => {
        let i = 0;
        function send() {
            if (i >= 2000000) return;
            let numbers = [];
            for (let j = 0; j < 200000; j++) {
                numbers.push(i + j);
            }
            ws.send(JSON.stringify({ action: 'check', numbers: numbers }));
            i += 200000;
            setTimeout(send, 250);
        }
        send();
    };

Accidentally sent way more than required, but it worked.

![](https://i.imgur.com/DGjYRvj.png)


**Key Learning and Takeaways**

- Web sockets are different from HTTP; learning how they work is useful for web challenges.
- Sending too much data at once can crash the connection; break it into smaller parts and add a delay.
- You don't need to fully know a programming language to write useful scripts experimenting helps.
- Automating tasks with code is often more effective than using manual tools like Burp.
- Code doesn’t need to be perfect, if it works, it works.


