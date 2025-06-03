---
layout: load_md
title: The White Circle | Nahamcon 2025 | SNAD Writeup
desc: Check out our writeup for SNAD for Nahamcon 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: SNAD
tags: "web, legend, js"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---


> Solved by Legend

In this challenge the website is having interaction if we click the website *sand* particles will fall off.
I check the source code and found the code for this where specific points were mentioned which we needed to trigger.

Tried a bunch of things and also tried to access the flag using `checkFlag()` but it didn't work so again made a script which will inject the points where the code is looking for.


    const targets = [
      { x: 367, y: 238, hue: 0 },
      { x: 412, y: 293, hue: 40 },
      { x: 291, y: 314, hue: 60 },
      { x: 392, y: 362, hue: 120 },
      { x: 454, y: 319, hue: 240 },
      { x: 349, y: 252, hue: 280 },
      { x: 433, y: 301, hue: 320 }
    ];
    
    for (let i = 0; i < targets.length; i++) {
      injectSand(targets[i].x, targets[i].y, targets[i].hue);
    }

Once the points are matched to where the code is expecting the checks are passed and flag is given.

![](https://i.imgur.com/KLnhP7m.png)


**Key Learning and Takeaways**

- Always check the source code it often includes useful logic or hardcoded values.
- Interactive web challenges may hide important behavior in JavaScript; inspect scripts, not just HTML.
- Reverse-engineering how the frontend works can help you figure out how to trigger backend logic.

