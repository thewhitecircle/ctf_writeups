# RE

## Authenticator

> Solved by chronocruz.exe

* Disassembling the binary using IDA we get the first code block

![](https://i.imgur.com/ifoeGHl.png)

* Here we can clearly see the “Alien ID: “ that is supposed to be the first input

![](https://i.imgur.com/ReAIN8e.png)

* To find the pin, we proceed down the code flow

![](https://i.imgur.com/ebv9Vul.png)

* We can see there’s a function named “checkpin” being called so we look at what its doing

![](https://i.imgur.com/WWwUY4d.png)

* It may get slightly difficult to understand what this code really means..
* We can use decompilers like Ghidra to try to convert this into something we can understand better.

![](https://i.imgur.com/kYm2D9l.png)

* Looking at the decompiled “checkpin” function, we can clearly see a XOR operation on a string.
* It’s safe to say that our pin must be the XOR of each character in this string with 9.
* With the help of this simple Python script we print the flag

![](https://i.imgur.com/m8xdTtn.png)

![](https://i.imgur.com/L1LwiNM.png)

## Passphrase

> Solved by chronocruz.exe

Disassembling the binary in IDA we reach the first code block where a certain portion of the code caught my eye

![](https://i.imgur.com/rCnB49a.png)

* So I wrote down the string given here

```
3xtr4t3rR3stR14L5_VS_hum4n5
```

* Tried using this string in the program and voila!

![](https://i.imgur.com/04dcCGF.png)