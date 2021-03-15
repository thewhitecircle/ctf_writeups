# misc

## eighth_circle

Challenge text - 

```
D'`r#LK\[}{{EUUTet,r*qo'nmlk5ihVB0S!>w<<)9xqYonsrqj0hPlkdcb(`Hd]#a`_A@VzZY;Qu8NMRQJn1MLKJCg*)ED=a$:?>7[;:981w/4-,P*p(L,%*)"!~}CB"!~}_uzs9wpotsrqj0Qmfkdcba'H^]\[Z~^W?[TSRWPt7MLKo2NMFj-IHG@dD&<;@?>76Z{9276/.R21q/.-&J*j(!E%$d"y?`_{ts9qpon4lTjohg-eMihg`&^cb[!_X@VzZ<RWVOTSLpP2HMFEDhBAFE>=BA:^8=6;:981Uvu-,10/(Lm%*)(!~D1
```

After googling “eight circle of hell cipher” we find something called Malbolge, a programming language.
We use a decoder for it and we get the 

https://malbolge.doleczek.pl/


```
flag - flag{bf201f669b8c4adf8b91f09165ec8c5c}
```

----------

## Prison Break

```
cat /just/out/of/reach/twh.txt -> No such file or directory i.e. real cat error
```

but if you try

```
cat /just/out/of/reach/flag.txt -> error changes into a custom one
```

script is detecting the keyword `flag`

![](https://i.imgur.com/TVn65zl.png)

----------

## Zenith

* Goal was to privesc to root and get the flag from root directory

* detecting privesc was easy because all i had to do was `sudo -l`

* we can execute `zenity` with sudo without password

* zenity is an application which generates GUI pop ups.

* but we have ssh, a simple workaround for this is to use `-X` commandline option of ssh which forwards gui applications to our machine, so using this we can execute zenity on remote server and popups will appear on our machine.

* now after reading manpage of zenity few times this command worked

```
zenity --text-info --filename "/root/.ssh/id_rsa"
```

![](https://i.imgur.com/M3C9zEb.png)

* and a pop up appears with private key of root <3

![](https://i.imgur.com/kse1aWc.png)
