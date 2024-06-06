---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Hunting License Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: re
challenge: Hunting License
tags: "re, ava, ghidra, gdb"
date: 2023-03-27T00:00:00+00:00
last_update: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Hunting License 
> Solved by Avantika(@iamavu)
    
> STOP! Adventurer, have you got an up to date relic hunting license? If you don't, you'll need to take the exam again before you'll be allowed passage into the spacelanes!

We are provided a binary called `license`  and a docker instance support to get the flag from the server

This challenge just essentially tests out your basics so pretty straight-forward walkthrough

When you run the binary you are greeted with a following prompt

![](https://i.imgur.com/awffQL5.png)


press `y` and move forward 

![](https://i.imgur.com/Xr5NE86.png)


so essentially we have to crack some passwords, alright time to `CTRL+C` and check what binary is and what it does
First thing I like to do is run `checksec` to check how binary is configured
We get following and the the thing that surprise is it doesn’t have PIE enabled

![](https://i.imgur.com/oIRrv7L.png)


Which means the binary will be loaded at same place in memory everytime and it’s not randomized every time you load the binary.
Coolio!
Let’s fire up the binary-dragon, ghidra and check what’s the code doing.
We got our function names intact, so going to the main function

![](https://i.imgur.com/PQ7vAMw.png)


it checks if we are ready or not and then runs `exam()` function which is the juicy function 
let’s have a look at the `exam()` function 

![](https://i.imgur.com/FlUGwCC.png)


well, looks pretty big but it’s checking with `strcmp()` if we enter the right value or not
first one is literally in the code, so the first password is pretty simple - `PasswordNumeroUno`

Second password is something reversed, now I could technically reverse it from the code/ghidra but let’s do the smart work and run the binary in gdb (pwndbg) and set a breakpoint at the address of where it asks us for the second password.
How and why can we do that, simple - NO PIE (read more about it if you are unfamiliar)

Copy the address from the ghidra for the statement where it asks us for the password, set the breakpoint (`break *0xAddress`) and hit run!

![](https://i.imgur.com/Jp1wBMz.png)


As we can see in the disassembly we can see the register holding the second compare value which is `0wTdr0wss4P`, but reversed i.e. `P4ssw0rdTw0` which is our second password.

Let’s have look at final password by the same trick we did above. Find the address from the ghidra, set breakpoint, and hit run and see the stack this time!

![](https://i.imgur.com/x5z2aC5.png)


As we can see the rsp pointing towards the address containing our password which is `ThirdAndFinal!!!`

Which does means that we solved but we need to do some couple of extra answers to question to solve it.

![](https://i.imgur.com/A0vFJjD.png)


Let’s go one by one

```
1. ELF, as this is a linux binary
2. Check architecture by running `file` command on this binary
3. run `ldd` to see the libraries used for various purposes
4. Get the address of the main function from ghidra
5. Check main function’s disassembly in ghidra and see how many puts call are there
6. Password One, we have it
7. Reversed form of Password Two
8. Password Two, we have it
9. Check the `exam()` function and in the `xor` statement, the fourth parameter in decimal form is our answer to this question
10. Password Three, we have it
```

We get our flag - `HTB{l1c3ns3_4cquir3d-hunt1ng_t1m3!}`
as always keep hacking : D


