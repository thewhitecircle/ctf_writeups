---
layout: load_md
title: The White Circle | Nahamcon 2025 | The Oddyssey Writeup
desc: Check out our writeup for The Oddyssey for Nahamcon 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: warmup
challenge: The Oddyssey
tags: "warmup, twh, scripting, bash"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---


> Solved by thewhiteh4t

This was a basic scripting challenge. It can be solved using other languages too, such as python, but I used bash as it was a more quick solution. When we connect to the given endpoint a chunk of text is received and a message to press enter to continue. After every chunk we were supposed to press enter and the next chunk of text is received.

So the idea was that we will get the flag either at the end of the text or somewhere in between. Initially I pressed enter manually few times, but it was like never ending, so I wrote the following infinite loop.


    > while true; do
        echo ''
        sleep 0.5
    done | nc challenge.nahamcon.com 32084

This is how we can automate enter key action in `netcat`, we just echo blank and use pipe to netcat. A delay is important as we want the next chunk to receive before pressing enter. The amount of delay depends on how fast the server is sending the response.

And below you can see the flag is hidden between text in a random chunk.


![](https://i.imgur.com/meAnptz.png)


This can be further improved by redirecting all the received text into a file so we can `grep` the flag later from the file, that way we don’t miss the flag.

**Key Learning and Takeaways**


- Automation is Your Best Friend : When it feels like something is never ending, that's your cue to stop manual labor and start scripting! Even a simple loop can save you a lot of time and frustration.
- The Importance of Delays : It's super important to match the server's response time. Even in real world testing delays are required so that we don’t affect the availability of the target.


