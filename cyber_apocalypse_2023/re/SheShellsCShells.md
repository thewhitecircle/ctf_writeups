---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | She Shells C Shells Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: re
challenge: She Shells C Shells
tags: "re, warlordsam, ghidra"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

##  She Shells C Shells
> Solved by warlordsam

Challenge description

![](https://i.imgur.com/0tDZYcp.png)


In this challenge we are given a ELF 64-bit LSB pie executable (not stripped).

![](https://i.imgur.com/AVU3W06.png)


Using ghidra , we can get the flag. Open the binary in  CodeBrowser and find the function called *func_flag.* 

Observing the code snippet from the func_flag ; we can see that  it uses memcmp to compare the user input and stored bits.

      fgets((char *)&local_118,0x100,stdin);
      for (local_c = 0; local_c < 0x4d; local_c = local_c + 1) {
        *(byte *)((long)&local_118 + (long)(int)local_c) =
             *(byte *)((long)&local_118 + (long)(int)local_c) ^ m1[(int)local_c];
      }
      local_14 = memcmp(&local_118,t,0x4d);
      if (local_14 == 0) {
        for (local_10 = 0; local_10 < 0x4d; local_10 = local_10 + 1) {
          *(byte *)((long)&local_118 + (long)(int)local_10) =
               *(byte *)((long)&local_118 + (long)(int)local_10) ^ m2[(int)local_10];
        }
        printf("Flag: %s\n",&local_118);
        uVar1 = 0;
      }
      else {
        uVar1 = 0xffffffff;
      }
      return uVar1;
    }
    

There are three arrays *m1, m2 and t* that we need to look at. The user input is stored in *m1* array and compared with the *t array* which is a stored block of password starting at memory address:0x55fb06ae0200 to check if we got the correct input. It performs XOR on each bit of both arrays.

To get the flag; We can directly see hex values of *t* and *m2* in *memcmp*. Total length is 77 characters. Each bit of *t  and  m2* undergo XOR  to form a flag with format of  HTB{ 

Copying all the hex values present in *t* and *m2;* and performing XOR using online calculator: https://xor.pw/ we can obtain the flag.

For Example: bits in t and m2 respectively are:

```
1. t: 2c 4a b7 
2. m2 : 64 1e f5 
```

Flag : HTB....so on 


