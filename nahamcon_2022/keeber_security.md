# Keeber Security Group

## Keeber 1
> Solved by: Starry-Lord

![](https://i.imgur.com/z09VgbV.png)

By searching for keeber security group on google we quickly found a valid domain at keebersecuritygroup.com, then finding the registrant name online gave the flag.

![](https://i.imgur.com/rrxhgDL.png)

----------

## Keeber 2
> Solved by: Starry-Lord

![](https://i.imgur.com/FEy7Elt.png)

We can check for past versions of most website, and we find they fired Tiffany Douglas:

![](https://i.imgur.com/3N5SVar.png)

----------

## Keeber 3
> Solved by: Starry-Lord

![](https://i.imgur.com/nTVVuT3.png)

Here is their github:

https://github.com/keebersecuritygroup

https://github.com/keebersecuritygroup/security-evaluation-workflow/commit/e76da63337cfabb12ea127af3f86168e9dd08428


We can see at this point in time a file called asana_secret.txt was uploaded to the github by mistake, Tiffany made a typo in the .gitignore file which ended up preventing asana_secret.tx from being commited (which doesn’t exist).
Looking up Asana, I read we can query other users e-mails if we invite them to a group we create. It didn’t help us here but still noticeable detail.
Researching more on asana, I discovered it has an API which allows to get information back with the right Authorization Header.

![](https://i.imgur.com/qcH4mkz.png)

----------

## Keeber 4
> Solved by: Starry-Lord

![](https://i.imgur.com/gNzQkHh.png)


To open this kbdx file we can use keepass2 on Kali Systems, after grabbing it from the github. 

To make a custom wordlist from public facing information I used cewl:

```
cewl https://raw.githubusercontent.com/keebersecuritygroup/security-evaluation-workflow/main/code_reviews.txt > code_reviews.txt 
```

I did so for each text files in the /security-evaluation-workflow/ repository.

Then I had to turn it into a crackable format with keepass2john:

```
starlord@HAL-9000:~/Bureau/Fun/Nahamcon2022/Keeber$ keepass2john ksg_passwd_db.kdbx 

ksg_passwd_db:$keepass$*2*58823528*0*d1aa5a09ccf3f75d30ea2d548ca045d28252c90adc8bf016bd444cbb3d6d5f65*580f6c41d95ea9407da649ee0312209f1686edf0b779458d57288ed7043c60ff*aec6b24ac45bf46d4b632d5e408799c7*4fa205b599089f79005e176c9c47690ffc58492169309a47613d4269a8ef2a52*f51a2a1f36f1ca1d10439aa78eccece46337274880f594f5a62a703f6007374f
```

![](https://i.imgur.com/kclUiYV.png)

```
password: craccurrelss
```

![](https://i.imgur.com/mH7mPvb.png)

![](https://i.imgur.com/AXs6oPQ.png)

----------

## Keeber 5
> Solved by: Starry-Lord

![](https://i.imgur.com/cvFWbR7.png)

Clone the repository /security-evaluation-workflow/ and check commit logs.

![](https://i.imgur.com/Qwbk4lM.png)

```
email: tif.hearts.science@gmail.com
```

----------

## Keeber 6
> Solved by: Starry-Lord

![](https://i.imgur.com/0vltGeV.png)

Lost a piece of my soul and made a yelp account, to look for reviews by e-mail.

![](https://i.imgur.com/diItmsv.png)

----------

## Keeber 7
> Solved too late by: Starry-Lord

![](https://i.imgur.com/wv3G9Hs.png)

![](https://i.imgur.com/D84HVl6.png)

```
e-mail: cheerios.fanatic1941@gmail.com
```

https://seon.io/resources/the-ultimate-guide-to-free-email-lookup-and-reverse-email-lookup-tools/


This online tool showed a mention about myspace, so I sacrificed another bit of my soul and made a MySpace account. This allows us to find the flag and a new username:


![](https://i.imgur.com/aZWdK8w.png)

----------

## Keeber 8
> Solved too late by: Starry-Lord

![](https://i.imgur.com/lowGp5C.png)

```
myspace username: cereal_lover1990
```

A quick search for the username with another [online tool](https://instantusername.com) reveals a matching user on pastebin.com:
 

```
https://pastebin.com/u/cereal_lover1990
```

![](https://i.imgur.com/WwcO2u9.png)

content of Chump List:

![](https://i.imgur.com/oK6PGzb.png)