---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Sealed Runes Writeup
desc: Check out our writeup for Sealed Runes for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: re
challenge: Sealed Runes
tags: "re, ava, ida"
date: 2025-03-29T00:00:00+00:00
last_modified_at: 2025-03-29T00:00:00+00:00
---

> Solved by avantika


![](https://i.imgur.com/wuPRcFZ.png)


Running the challenge, we are asked for input and if given wrong one, it rejects us (sad life). Let's try opening up the binary in IDA and take a look at decompiled code.


    int __fastcall main(int argc, const char **argv, const char **envp)
    {
      char input\[56]; // [rsp+0h\] [rbp-40h] BYREF
      unsigned __int64 v5; // \[rsp+38h\] [rbp-8h]
    
      v5 = __readfsqword(0x28u);
      anti_debug(argc, argv, envp);
      display_rune();
      puts(a134m_0);
      printf("Enter the incantation to reveal its secret: ");
      __isoc99_scanf("%49s", input);
      check_input(input);
      return 0;
    }

This is the main function, which simply asks us for our inputs. Decompiling the function `check_input()` we get the following code


    void __fastcall check_input(const char *a1)
    {
      __int64 v1; // rax
      char *s2; // \[rsp+18h\] [rbp-8h]
    
      s2 = (char *)decode_secret();
      if ( !strcmp(a1, s2) )
      {
        puts(a132mtheRuneGlo);
        v1 = decode_flag();
        printf("\x1B[1;33m%s\x1B[0m\n", (const char *)(v1 + 1));
      }
      else
      {
        puts("\x1B[1;31mThe rune rejects your words... Try again.\x1B[0m");
      }
      free(s2);
    }

from what it seems, it decodes the flag and then prints it if we input the right string, lets look at the `decode_flag()`function


    const char *decode_flag()
    {
      const char *v1; // \[rsp+8h\] [rbp-8h]
    
      v1 = (const char *)base64_decode(flag);
      reverse_str(v1);
      return v1;
    }

looks like we found the `flag`, lets us check the string


![](https://i.imgur.com/MKQJh1X.png)


Base64 decoding this string (`LmB9ZDNsNDN2M3JfYzFnNG1fM251cntCVEhgIHNpIGxsZXBzIHRlcmNlcyBlaFQ=`)gives us a string which looks like if reversed would give us the flag, so cyberchef it is!


![](https://i.imgur.com/d7MCFlA.png)


`HTB{run3_m4g1c_r3v34l3d}` is our flag.


