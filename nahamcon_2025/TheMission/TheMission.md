---
layout: load_md
title: The White Circle | Nahamcon 2025 | The Mission Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: TheMission
challenge: The Mission
tags: "web, starry, api, fuzzing, graphql, waf bypass, misconfig, ssrf"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>

## The Mission
> Solved by Starry-lord

<div class="ctf-cat-bar">
    <a href="#flag-1" data-augmented-ui="tl-clip br-clip both" class="aui-theme-btn ctf-cat-btn white-text" onclick="">Flag 1</a>
    <a href="#flag-2" data-augmented-ui="tl-clip br-clip both" class="aui-theme-btn ctf-cat-btn white-text" onclick="">Flag 2</a>
    <a href="#flag-3" data-augmented-ui="tl-clip br-clip both" class="aui-theme-btn ctf-cat-btn white-text" onclick="">Flag 3</a>
    <a href="#flag-4" data-augmented-ui="tl-clip br-clip both" class="aui-theme-btn ctf-cat-btn white-text" onclick="">Flag 4</a>
    <a href="#flag-5" data-augmented-ui="tl-clip br-clip both" class="aui-theme-btn ctf-cat-btn white-text" onclick="">Flag 5</a>
    <a href="#flag-6" data-augmented-ui="tl-clip br-clip both" class="aui-theme-btn ctf-cat-btn white-text" onclick="">Flag 6</a>
</div>


You can find the author's French translation on <a href="https://synthweb.ch/fr/blog/the-mission-nahamcon-ctf-2025" target="_blank">Synthweb the Swiss web agency | Your IT specialist in Geneva</a>


![](https://i.imgur.com/3vCjEqA.png)


This challenge was in 6 parts, made by Adam Langley from [HackingHub](https://hackinghub.io). The goal was to mark Stök’s latest bug report as a duplicate, in order to make ours accepted. It holds a special place in my heart because it was my first time ever scoring a “first blood” in a competition! 
🩸 
Because I expect a few non-technical readers, I will try to be as demonstrative as possible.

### Flag 1

Usually, we discover a webpage where a few functionalities are present, and the goal here is to try them all and try to deduct how they are implemented, in order to potentially access stuff we shouldn’t be able to or break them. We also get a set of credentials, and a wordlist we can use in order to avoid massive amounts of requests from using public directory fuzzing wordlists with our fuzzing tools. Here is the website:


![](https://i.imgur.com/HaVcUkt.png)


This is a bug bounty platform with a leaderboard and a login. The top hackers page is valuable, as it shows us potential users we could have in this database.


![](https://i.imgur.com/DpkKKXi.png)


Before logging in, I like to check if the website has something to hide from web crawling robots amongst a few other things, and that’s where we find our first flag :


![](https://i.imgur.com/SR4KWqv.png)


### Flag 2

So here, the website wants to hide the `/internal-dash` page from crawling bots, which is a good reason to make me curious about it. It’s an admin portal which expects a classic username and password combo in a POST request, which we don’t have.


![](https://i.imgur.com/9ETuZan.png)


Now is time to visit `/login` to login into the website with the provided credentials `hacker:password123` (in the real world there would most probably be a /register page somewhere). We can see a dashboard, with a Settings and Logout page. At this point, I switched to use `BurpSuite`, a common pentesting tool to basically catch all web traffic coming from the website, which revealed a couple interesting things.


![](https://i.imgur.com/RIuORFs.png)


As soon as we connect to the dashboard there is a call to `/api/v2/reports` with a `user_id` parameter, which returns the list of our user’s reports. This could be interesting to query other reports if we ever got our hands on other user_ids. The settings page is also very interesting, as it reveals a `graphql` endpoint, which is another potential opportunity to query things we shouldn’t. More on that in `Flag 4`.


![](https://i.imgur.com/cYtBlTt.png)


By now we can start fuzzing to see if we can identify more endpoints with that wordlist:


![](https://i.imgur.com/5O0kXas.png)


There are all the pages we already saw until now, uploads and assets folders returning a 403 Forbidden response. But we also got a hit for a v1 of the API, which was not in use anymore.


![](https://i.imgur.com/mVyoFyS.png)


“Please don’t use it” sounds to me like an invitation.  This was a good time to try and query some interesting endpoints for common “open java development kit” based web applications, such as these:

`/jmx-console` (JMX)

`/manager/html` (Tomcat Manager)

`/actuator` (Spring Boot Actuator endpoints)

`/console` (H2, Derby, or other embedded DBs)

I got lucky with `/actuator` which confirmed we are working with the Spring Boot Framework.


![](https://i.imgur.com/IC0zpoQ.png)


Bypassing the WAF was not our most difficult task, as a simple URL encode tricked it and revealed flag 2, which I was the first to find in the competition!


![](https://i.imgur.com/hnNKSz9.png)


### Flag 3

We can see a reference to `/api/v1/actuator/heapdump` directly in the actuator. The content of this file is a memory dump of a POST request made from the user `inti`, with a JSON Web token Bearer header, which allowed to query a `/api/v1/internal-dashboard/token` endpoint.

`/api/v1/actuator/heapdump` content :


    0x00007f9b3c1a2e80: 6a 61 76 61 2e 6c 61 6e 67 2e 53 74 72 69 6e 67  java.lang.String
    0x00007f9b3c1a2e90: 40 35 63 39 65 66 61 34 0a 5b 52 75 6e 74 69 6d  @5c9efa4.[Runtime
    0x00007f9b3c1a2ea0: 43 6c 61 73 73 65 73 5d 20 64 65 62 75 67 20 6d  Classes] debug m
    0x00007f9b3c1a2eb0: 6f 64 65 20 65 6e 61 62 6c 65 64 0a 00 70 72 6f  ode enabled..pro
    0x00007f9b3c1a2ec0: 63 65 73 73 4d 61 6e 61 67 65 72 3a 20 6a 61 76  cessManager: jav
    0x00007f9b3c1a2ed0: 61 2e 75 74 69 6c 2e 63 6f 6e 63 75 72 72 65 6e  a.util.concurrent
    0x00007f9b3c1a2ee0: 74 2e 46 6f 72 6b 4a 6f 69 6e 50 6f 6f 6c 24 57  t.ForkJoinPool$W
    0x00007f9b3c1a2ef0: 6f 72 6b 65 72 40 31 61 66 66 38 66 66 0a 0a 70  orker@1aff8ff..p
    0x00007f9b3c1a2f00: 70 6f 73 74 20 2f 61 70 69 2f 76 31 2f 69 6e 74  post /api/v1/int
    0x00007f9b3c1a2f10: 65 72 6e 61 6c 2d 64 61 73 68 62 6f 61 72 64 2f  ernal-dashboard/
    0x00007f9b3c1a2f20: 74 6f 6b 65 6e 20 48 54 54 50 2f 31 2e 31 0d 0a  token HTTP/1.1..
    0x00007f9b3c1a2f30: 41 75 74 68 6f 72 69 7a 61 74 69 6f 6e 3a 20 42  Authorization: B
    0x00007f9b3c1a2f40: 65 61 72 65 72 20 65 79 4a 68 62 47 63 69 4f 69  earer eyJhbGciOi
    0x00007f9b3c1a2f50: 4A 49 55 7A 49 31 4E 69 49 73 49 6E 52 35 63 43  JIUzI1NiIsInR5cC
    0x00007f9b3c1a2f60: 49 36 49 6B 70 58 56 43 4A 39 2E 65 79 4A 31 63  I6IkpXVCJ9.eyJ1c
    0x00007f9b3c1a2f70: 32 56 79 62 6D 46 74 5A 53 49 36 49 6D 6C 75 64  2VybmFtZSI6Imlud
    0x00007f9b3c1a2f80: 47 6B 69 66 51 2E 59 65 71 76 66 51 37 4C 32 35  GkifQ.YeqvfQ7L25
    0x00007f9b3c1a2f90: 6F 68 68 77 42 45 35 54 70 6D 71 6F 32 5F 35 4D  ohhwBE5Tpmqo2_5M
    0x00007f9b3c1a2fa0: 68 71 79 4F 43 58 45 37 54 39 62 47 38 39 35 55  hqyOCXE7T9bG895U
    0x00007f9b3c1a2fb0: 6B 0d 0a 48 6F 73 74 3A 20 69 6E 74 65 72 6E 61  k..Host: interna
    0x00007f9b3c1a2fc0: 6C 2D 74 65 73 74 69 6E 67 2D 61 70 70 73 0d 0a  l-testing-apps..
    0x00007f9b3c1a2fd0: 43 6f 6e 74 65 6e 74 2d 54 79 70 65 3a 20 61 70  Content-Type: ap
    0x00007f9b3c1a2fe0: 70 6c 69 63 61 74 69 6f 6e 2f 6a 73 6f 6e 0d 0a  plication/json..
    0x00007f9b3c1a2ff0: 43 6f 6e 6e 65 63 74 69 6f 6e 3a 20 6b 65 65 70  Connection: keep
    0x00007f9b3c1a3000: 2d 61 6c 69 76 65 0d 0a 0d 0a 7b 22 75 73 65 72  -alive....{"user
    0x00007f9b3c1a3010: 6e 61 6d 65 22 3a 22 69 6e 74 69 22 7d 00 6a 61  name":"inti"}..ja
    0x00007f9b3c1a3020: 76 61 2e 6e 65 74 2e 55 52 4c 43 6c 61 73 73 2e  va.net.URLClass.
    0x00007f9b3c1a3030: 63 6f 6d 2f 65 78 61 6d 70 6c 65 2f 41 70 70 6c  com/example/Appl

Reproducing this request for myself, we managed to get a token from the API!


![](https://i.imgur.com/1bQeyRW.png)


This was definitely the way forward, the only thing left was to figure out how to use this token. It was definitely pointing towards a way to bypass the /internal-dash login page we found in the beginning, but how exactly was the most difficult part of this challenge for me. This token could have been 2 different things: 

- A JSON web token secret to allow us to make JWT for other users and pass it as a header (but nothing I did worked)
- A cookie value for authentication in the /internal-dash.

That’s when [thewhiteh4t](https://thewhiteh4t.github.io) got the brilliant idea to query the `/internal-dash/logout` page, which reveals the name of the cookie:


![](https://i.imgur.com/4ke7joj.png)


With this we were able to bypass logging in the internal-dash without posting or guessing credentials! Passing the retrieved token as a cookie gave us flag 3 `int-token=<token>`.


![](https://i.imgur.com/0866XKt.png)


### Flag 4

So now we can look up reports by report ID. We had found our one report ID in our Dashboard upon using the provided credentials, but trying it revealed we don’t have permission.


![](https://i.imgur.com/lrW6XQH.png)


We will circle back on this later in flag 5, in order to keep the flag order. Remember this `graphql` endpoint we saw when visiting the Settings page from the user dashboard? Perfect time to look into it.


![](https://i.imgur.com/dbcXc59.png)


We even have the introspection query used directly in the backend, which makes our life even easier to determine what functions can be used with this `Apollo Graphql` server.

The page shows us a couple different queries that we could do without any proper authentication:


![](https://i.imgur.com/octAVqX.png)


The first query required to pass a variable `userId` so passing ours made it work:


![](https://i.imgur.com/EcSmMsa.png)


But the second query wasn’t asking for anything, which allowed us to retrieve the full list of user IDs, as well as Flag 4:


![](https://i.imgur.com/bLTmDuR.png)


### Flag 5

Now we had a few things we could put together in order to make this work, but still missing permission to use the lookup feature in the internal dashboard from Flag 3. Playing around with the POST request with our `BurpSuite` repeater, we could eventually get telling messages about what was going on behind the scene:

![](https://i.imgur.com/WuOFdKa.png)


I added a / with random character to that report ID, and got this telling message about Unknown Endpoint, which was a strong indicator that this ID value was being treated as an internal URL.

Sending a URL encoded null byte gives us a null response, indicating the backend was probably not built to parse this properly, a second indicator that we were supposed to find endpoints.

![](https://i.imgur.com/KblT82S.png)


The third and final definitive confirmation was given to me by injecting a UNICODE null byte `\u0000` to the report ID. 


![](https://i.imgur.com/ngKtmdC.png)


We are definitely working with cURL making a call to `http://127.0.0.1` so we can definitely try other endpoints. Trying `/etc/passwd` didn’t work, but trying the `/api/` endpoint revealed some more internal methods!


![](https://i.imgur.com/ScUkgUP.png)


Internally, it was calling `http://127.0.0.1/my-reports/<report-id>`so we could try the search endpoint now:


![](https://i.imgur.com/GHbYLlY.png)


Passing `q=<report-id>` made it work, and allowed us to retrieve the change hash that we could use to change statuses, as shown in the script below (found on the internal-dash page’s HTML). We could leverage the `/api/v2/reports?user_id=<user_id>` endpoint to get the report ID for Stök’s report which was posted before ours. We had all the user IDs from the Apollo server at the `graphql` endpoint, so we could then retrieve the wanted report IDs.


![](https://i.imgur.com/IpUciPo.png)


Big win here! It was time to get Stök’s Pending report as well and try to change their statuses as described in the code below.

Content of `/internal-dash` script logic:


    <script>
        // Store the change_hash globally
        let currentChangeHash = null;
    
        document.getElementById('reportForm').addEventListener('submit', async (e) => {
            e.preventDefault();
    
            const reportId = document.getElementById('reportId').value;
            const errorMessage = document.getElementById('errorMessage');
            const reportCard = document.getElementById('reportCard');
    
            // Reset previous states
            errorMessage.classList.remove('visible');
            reportCard.classList.remove('visible');
            currentChangeHash = null; // Reset change_hash
    
            try {
                const response = await fetch('/internal-dash/api/report', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({ id: reportId })
                });
    
                if (response.status === 404) {
                    throw new Error('Report not found');
                } else if (response.status === 403) {
                    throw new Error('You do not have access to this report');
                } else if (!response.ok) {
                    throw new Error('An error occurred while fetching the report');
                }
    
                const report = await response.json();
    
                // Store the change_hash
                currentChangeHash = report.change_hash;
    
                // Update the report card with the data
                document.getElementById('reportIdDisplay').textContent = report.id;
                document.getElementById('reportTitle').textContent = report.title;
                document.getElementById('reportCompany').textContent = report.company;
                document.getElementById('reportStatusDetail').textContent = report.status;
                document.getElementById('reportPaid').textContent =
                    new Intl.NumberFormat('en-US', {
                        style: 'currency',
                        currency: 'USD'
                    }).format(report.paid);
    
                // Set status class and select current status
                const statusElement = document.getElementById('reportStatus');
                statusElement.textContent = report.status;
                statusElement.className = 'status ' + report.status.toLowerCase();
    
                // Set the current status in the dropdown
                document.getElementById('statusSelect').value = report.status;
    
                // Show the report card
                reportCard.classList.add('visible');
    
            } catch (error) {
                errorMessage.textContent = error.message;
                errorMessage.classList.add('visible');
            }
        });
    
        // Handle status updates
        document.getElementById('updateStatus').addEventListener('click', async () => {
            const statusSelect = document.getElementById('statusSelect');
            const newStatus = statusSelect.value;
    
            if (!newStatus) {
                alert('Please select a status');
                return;
            }
    
            if (!currentChangeHash) {
                const errorMessage = document.getElementById('errorMessage');
                errorMessage.textContent = 'Invalid authorization. Please reload the report.';
                errorMessage.style.backgroundColor = '#fff5f5';
                errorMessage.style.color = '#c53030';
                errorMessage.classList.add('visible');
                return;
            }
    
            const reportId = document.getElementById('reportIdDisplay').textContent;
    
            try {
                const response = await fetch('/internal-dash/api/report/status', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        id: reportId,
                        status: newStatus,
                        change_hash: currentChangeHash
                    })
                });
    
                if (response.status === 403) {
                    throw new Error('Unauthorized to update status');
                } else if (!response.ok) {
                    throw new Error('Failed to update status');
                }
    
                // Update the status display
                const statusElement = document.getElementById('reportStatus');
                const statusDetail = document.getElementById('reportStatusDetail');
    
                statusElement.textContent = newStatus;
                statusElement.className = 'status ' + newStatus.toLowerCase();
                statusDetail.textContent = newStatus;
    
                // Show success message
                const errorMessage = document.getElementById('errorMessage');
                errorMessage.textContent = 'Status updated successfully';
                errorMessage.style.backgroundColor = '#f0fff4';
                errorMessage.style.color = '#2f855a';
                errorMessage.classList.add('visible');
    
                // Hide success message after 3 seconds
                setTimeout(() => {
                    errorMessage.classList.remove('visible');
                }, 3000);
    
            } catch (error) {
                const errorMessage = document.getElementById('errorMessage');
                errorMessage.textContent = error.message;
                errorMessage.style.backgroundColor = '#fff5f5';
                errorMessage.style.color = '#c53030';
                errorMessage.classList.add('visible');
            }
        });

We can see a call to a new endpoint in there, specifically a POST request to `/internal-dash/api/report/status` with the JSON variables: 


- id
- status (target status to change the report into)
- the change_hash for the targeted report


    const response = await fetch('/internal-dash/api/report/status', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        id: reportId,
                        status: newStatus,
                        change_hash: currentChangeHash
                    })
                });


![](https://i.imgur.com/cGxuf7X.png)


This ID is the one for Stök’s report, because it was not possible to update ours directly. So marking it as a duplicate opened the way to mark ours as accepted!


![](https://i.imgur.com/twFWAKu.png)


Then back to our user Dashboard, we finally found flag 5 along with a hefty bounty! 🤑 


![](https://i.imgur.com/jKQeqnS.png)

> Mission Accomplished!


### Flag 6

There was an AI chatbot in the bottom right of the dashboard, which we could have a conversation with.

Here is how it went for me :


![](https://i.imgur.com/EK1yd5t.png)



![](https://i.imgur.com/HkvKNqU.png)


😎 

