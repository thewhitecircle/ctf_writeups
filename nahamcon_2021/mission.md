# Mission

## Bionic

flag is in https://constellations.page/robots.txt

next target url : https://constellations.page/meet-the-team.html

source page mentions `VELA`, with the following context
```
Vela, can we please stop sharing our version control software out on the public internet
```

----------

## RECON

Github : https://github.com/constellations-git

Twitter : https://twitter.com/C0NST3LLAT10NS

there are 0 public repositories but there is one member :

https://github.com/gusrodry

in his followers list there is another account related to constellation

hercules : https://github.com/HerculesScox

in his account there is one repo created by him which is interesting

repo : https://github.com/HerculesScox/maintenance

website mentions “YouTube” and “Podcast” but both are not linked!

Email for hercules : herculesscoxland@yahoo.com

https://githubmemory.com/@HerculesScox

----------

## Gus

https://github.com/gusrodry/development/blob/master/config/.ssh/flag.txt

**along with the gus flag here we also get ssh private and public keys!**

**in the public key we can see “john@xps15”**

----------

## Meet The Team

flag is in a tweet on the twitter linked above

----------

## hercules

flag is in “connect.sh” in his repo : https://github.com/HerculesScox/maintenance/blob/main/connect.sh

along with the flag we can also see he that he used “sshpass” in this file, sshpass is a program used to auto login into ssh by supplying the password in command line instead of manually entering it everytime

```
hercules:starstruckherc
```

with these credentials we can SSH into DEGRADE challenge!

----------

## Leo

Directory search on https://constellations.page reveals `/.git/` directory

we dont have access to this repository because its not public on github so we can use a nice tool to dump directly from the website!

https://github.com/internetwache/GitTools

then if we check `git log` we can see full name of leo

![](https://i.imgur.com/R8hJZXS.png)

Instagram : `@_leorison`

there is a QR Code in one of the images 

```
flag{636db5f4f0e36908a4f1a4edc5b0676e} 
    
A password for Leo is constelleorising
```

we found flag and another creds!

----------

## Lyra and Orion

if we check the “meet the team” commit we can see names of all employees!

```
git show 4c88ac1c56fe228267cf415c3ef87d7c3b8abd60
```

![](https://i.imgur.com/uZKpNSE.png)

```
Orion Morra
Lyra Patte
Gemini Coley
Vela Leray
Pavo Welly
```

On twitter we can find Lyra’s profile : https://twitter.com/LyraPatte/with_replies

and she has linked one of the website links : https://constellations.page/constellations-documents/1/

`/1/` lets try more…on `/5/`

![](https://i.imgur.com/jRl29Un.png)

Once again on twitter we can find orion at : https://twitter.com/OrionMorra/with_replies

he has posted two useful pictures

https://twitter.com/OrionMorra/status/1363789936219082756/

AND

https://twitter.com/OrionMorra/status/1370730836736274433/


```
flag{0bcffb17cbcbf4359a42ec45d0ccaf2
```

```
orion:stars4love4life
```

another set of creds

----------

## Hydraulic

This one was straight forward, we dont know the username and we dont know the password, we just had to bruteforce

![](https://i.imgur.com/knOmRhA.png)

ssh login and cat flag.txt

----------
