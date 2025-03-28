---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Encrypted Scroll Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: re
challenge: Encrypted Scroll
tags: "re, ava, ida"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>

## Encrypted Scroll 
> Solved by avantika


![](https://i.imgur.com/KqQ9Bw1.png)


running the chanllege, we are asked for a input, giving the wrong input, we see it rejects us (sad life)
let us decompile the challenge using IDA


    int __fastcall main(int argc, const char **argv, const char **envp)
    {
      char v4\[56]; // [rsp+0h\] [rbp-40h] BYREF
      unsigned __int64 v5; // \[rsp+38h\] [rbp-8h]
    
      v5 = __readfsqword(0x28u);
      anti_debug(argc, argv, envp);
      display_scroll();
      printf(format);
      __isoc99_scanf("%49s", v4);
      decrypt_message(v4);
      return 0;
    }  

this is the main function, which seems to get our input and then decrypt it using `decrypt_message()`, lets decompile that function


    unsigned __int64 __fastcall decrypt_message(const char *a1)
    {
      int i; // \[rsp+1Ch\] [rbp-34h]
      char s2\[40]; // [rsp+20h\] [rbp-30h] BYREF
      unsigned __int64 v4; // \[rsp+48h\] [rbp-8h]
    
      v4 = __readfsqword(0x28u);
      strcpy(s2, "IUC|t2nqm4`gm5h`5s2uin4u2d~");
      for ( i = 0; s2[i]; ++i )
        --s2[i];
      if ( !strcmp(a1, s2) )
        puts("The Dragon's Heart is hidden beneath the Eternal Flame in Eldoria.");
      else
        puts("The scroll remains unreadable... Try again.");
      return v4 - __readfsqword(0x28u);
    }

we have a string, which goes under certain operation and then is compared with our input, i have written a python script to reverse that operation and get us the flag!


    enc = list('IUC|t2nqm4`gm5h`5s2uin4u2d~')
    
    flag = []
    for _ in enc:
        flag.append(chr(ord(_) - 1))
    print("".join(flag))

running this script, we get the flag
`HTB{s1mpl3_fl4g_4r1thm3t1c}` is our flag.


