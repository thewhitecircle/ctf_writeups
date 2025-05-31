---
layout: load_md
title: The White Circle | Nahamcon 2025 | My Third CTF Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: My Third CTF
tags: "web, twh, rot, fuzzing"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>

## My Third CTF
> Solved by thewhiteh4t

Similar to second variant of this challenge, we again had to perform directory enumeration but this time it was nested. That means nested directories. I used the same wordlist I created in second variant, so I will skip that part, if you are looking for that please read the writeup for second variant.


![](https://i.imgur.com/jYbH1Iy.png)


Got first hit, but we get 403 on this, I could not find anything after poking around so I finally tried to enumerate nested directories in the one I found and got another hit!


![](https://i.imgur.com/7yYPqtg.png)


After several iterations of the same process I finally landed on the flag directory! 


![](https://i.imgur.com/O6KpGaR.png)


**Key Learning and Takeaways**


- Thinking beyond 403 : After poking around a bit and realizing the challenge was likely nested was the key. So, instead of giving up on the 403 directory, we started enumerating inside it!
- Try Harder : Sometimes, the solution isn't immediate, and you just have to keep digging, keep iterating, and trust your instinct and tools to eventually find that hidden gem. Overtime with experience both your instinct and tooling will improve.

