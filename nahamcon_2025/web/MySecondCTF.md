---
layout: load_md
title: The White Circle | Nahamcon 2025 | My Second CTF Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: My Second CTF
tags: "web, twh, rot, fuzzing"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>


## My Second CTF
> Solved by thewhiteh4t

Similar to first variant of this challenge, we need to again use ROT cipher but differently. First I tried to use the previous wordlist, but that did not return anything. Then I figured out that this time we were supposed to perform directory enumeration as a wordlist was provided for this one. For ROTting each word I wrote a python script : 


    #!/usr/bin/env python3
    
    import sys
    
    rot_count = int(sys.argv[1])
    
    
    def rot(word):
        result = ''
        for char in word:
            if char.isalpha():
                base = ord('a') if char.islower() else ord('A')
                result += chr((ord(char) - base + rot_count) % 26 + base)
            else:
                result += char
        return result
    
    
    with open('wordlist.txt') as f:
        for line in f:
            word = line.strip()
            print(rot(word))

Yes the script can be improved in many ways, but again this is not a coding competition…


![](https://i.imgur.com/inJEJP1.png)


After that I performed directory enumeration using [FinalRecon](https://github.com/thewhiteh4t/FinalRecon), alternatively you can use `gobuster` or `ffuf` to achieve same results : 


![](https://i.imgur.com/VGn3p63.png)


Now here it took an interesting turn as this was an API endpoint and a correct parameter was required


![](https://i.imgur.com/dw8wZrp.png)


For this task I used [ffuf](https://github.com/ffuf/ffuf) to fuzz the parameter with the wordlist I created in previous stage : 


![](https://i.imgur.com/6cNin1z.png)


And got a hit! Below you can see that size is different for one request, which suggests a change :


![](https://i.imgur.com/apdgt8f.png)



![](https://i.imgur.com/Dt23ZQu.png)


**Key Learning and Takeaways**


- Scripting : This was the perfect scenario to whip up a quick Python script. It doesn't need to be pretty or perfect, but it needs to work and automate the work. 
- Directory Enumeration is Key : Once we had our ROT encoded directory wordlist, it was time for some serious brute forcing. Tools like FinalRecon (or gobuster/ffuf) are indispensable here.
- The API Twist : Finding a ROTted directory wasn't enough, it turned out to be an API endpoint. This immediately tells you that you need to send parameters. When status codes are same then we need to inspect a change in the response size.

