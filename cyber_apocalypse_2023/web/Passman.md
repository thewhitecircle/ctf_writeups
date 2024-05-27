---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Passman Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: web
challenge: Passman
tags: "web, starry, graphql"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Passman
> Solved by Starry-Lord


![](https://i.imgur.com/DL7HClI.png)


This app meets us with another login page, where we can also register an account.

![login, register, login again](https://i.imgur.com/KRwOPL4.png)


Checking the network tab actually showed a familiar name:


![graphql endpoint](https://i.imgur.com/IhMS5D3.png)


We can go to https://ivangoncharov.github.io/graphql-voyager/ in order to grab the infamous introspection query for graphql, which essentially returns all the content of the graphql schema. Click on “change schema”, then select the introspection tab and click on “Copy Introspection Query” to get the query to your clipboard.

Now we need to send it to this graphql endpoint but since its set to accept strings, we need to get rid of the new lines:


![remove new lines](https://i.imgur.com/BJOnVR4.png)


Then sending the request through BurpSuite’s repeater tab to the /graphql endpoint returns the full introspection as expected:


![graphql introspection](https://i.imgur.com/2Ytt52V.png)


With this data, we can go back to the graphql voyager tool and paste it in the box below the “Copy introspection query” button. This will give us a good sense of what data is built into this endpoint:


![graphql voyager](https://i.imgur.com/A3obPEf.png)


There’s nothing much about this Query, except returning the phrases owned by the logged user. But there were actual “Mutation” queries used when logging in, registering a new user, or adding a new phrase. It turned out there was another function (or mutation) allowing us to change a supplied user password, which seems like a bad thing to leave laying around unused.


![Mutations or available functions](https://i.imgur.com/z3upnC8.png)


UpdatePassword was the ticket to the flag, but first we needed to syntax it properly. I gladly used the content of the login graphql request shown below:


![LoginUser graphql query](https://i.imgur.com/ZFZL9yl.png)


From there it was just a matter of editing the request properly and updating the admin password with a cool password:


![Winning query](https://i.imgur.com/SBpvrSR.png)


After logging in with the admin account and the new password, we can see the flag in the password of the note:


![flag in Phrase password](https://i.imgur.com/8JmPbOA.png)



    HTB{1d0r5_4r3_s1mpl3_4nd_1mp4ctful!!}

