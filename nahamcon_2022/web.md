# web

## Jurrassic Park
> Solved By : Starry-Lord

This easy challenge made me (finally) realise John Hammond has the same name as the Owner of the Jurrassic Park in the first movies. 

Pretty cool site by the way:

![](https://i.imgur.com/ISh20Ne.png)

![](https://i.imgur.com/q5sFVil.png)

content of /robots.txt

```
User-agent: *
Disallow: /ingen/
```

The flag was here http://challenge.nahamcon.com/ingen/flag.txt

----------

## EXtravagant

> Solved By : nigamelastic
![](https://i.imgur.com/rmw9shq.png)

The challenge mentions the following:

```
The flag is in /var/www
```

on accessing the website we see a normal interface with xml parsing as a service

![](https://i.imgur.com/iWDrdSZ.png)

from the mentioning of XML it seems that this might be an XXE

Since we already know the locaiton of the flag I used the following payload:

![](https://i.imgur.com/sqelqWg.png)

I simply uploaded it to the trial tab:

![](https://i.imgur.com/h9WG0EH.png)

![](https://i.imgur.com/90bhiq3.png)

and then used view XML tab to view my xml

![](https://i.imgur.com/z5PUs40.png)

This would give flag

![](https://i.imgur.com/Tp2Wy2s.png)

----------

## Personnel
> Solved By : nigamelastic

The Challenge contains a python file,
On opening the python file u can clearly see som regex fu going on:

![](https://i.imgur.com/mvSfYju.png)

going through the code and testing it on the live link, u can see that it ignores the first letter if its upper case, and makes a logic around it. Its better if u view it in a debugging tool , I use regex101

![](https://i.imgur.com/XuhDSU4.png)

I also went through the official documentation (https://docs.python.org/3/library/re.html) to see if there are any special characters that can be used. the most interesting one was `|`
As per the documentation:

```
|
A|B, where A and B can be arbitrary REs, creates a regular expression that will match either A or B. An arbitrary number of REs can be separated by the '|' in this way. This can be used inside groups (see below) as well. As the target string is scanned, REs separated by '|' are tried from left to right. When one pattern completely matches, that branch is accepted. This means that once A matches, B will not be tested further, even if it would produce a longer overall match. In other words, the '|' operator is never greedy. To match a literal '|', use \|, or enclose it inside a character class, as in [|].
```

Once u go through it with a `flag{randomString}` u will find that the following regex would allow it. 

![](https://i.imgur.com/DN7cuJb.png)

So now we remove our initial regex and just add the ones we used aka `|flag{.*}|`
which gives u the flag

![](https://i.imgur.com/DrWLAJy.png)