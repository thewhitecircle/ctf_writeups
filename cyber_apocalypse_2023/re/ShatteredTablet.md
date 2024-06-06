---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Shattered Tablet Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: re
challenge: Shattered Tablet
tags: "re, starry, ghidra"
date: 2023-03-27T00:00:00+00:00
last_update: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Shattered Tablet
> Solved by Starry-Lord


![](https://i.imgur.com/oPaxFQs.png)


This challenge gave an ELF file so after basic identification, I tried to run it. When you run the file the tablet asks for an input and returns “no, not that”:

![](https://i.imgur.com/elH0lhM.png)


I decided to fire up ghidra, hopefully looking for what the expected input could be, what it would be compared against or anything relevant. Sure enough, by checking the main function, I found this code “explicitly” showing it was expecting the flag as input.


    undefined8 main(void)
    
    {
      undefined8 local_48;
      undefined8 local_40;
      undefined8 local_38;
      undefined8 local_30;
      undefined8 local_28;
      undefined8 local_20;
      undefined8 local_18;
      undefined8 local_10;
      
      local_48 = 0;
      local_40 = 0;
      local_38 = 0;
      local_30 = 0;
      local_28 = 0;
      local_20 = 0;
      local_18 = 0;
      local_10 = 0;
      printf("Hmmmm... I think the tablet says: ");
      fgets((char *)&local_48,0x40,stdin);
      if (((((((((local_30._7_1_ == 'p') && (local_48._1_1_ == 'T')) && (local_48._7_1_ == 'k')) &&
              ((local_28._4_1_ == 'd' && (local_40._3_1_ == '4')))) &&
             ((local_38._4_1_ == 'e' && ((local_40._2_1_ == '_' && ((char)local_48 == 'H')))))) &&
            (local_28._2_1_ == 'r')) &&
           ((((local_28._3_1_ == '3' && (local_30._1_1_ == '_')) && (local_48._2_1_ == 'B')) &&
            (((local_30._5_1_ == 'r' && (local_48._3_1_ == '{')) &&
             ((local_30._2_1_ == 'b' && ((local_48._5_1_ == 'r' && (local_40._5_1_ == '4')))))))))) &&
          (((local_30._6_1_ == '3' &&
            (((local_38._3_1_ == 'v' && (local_40._4_1_ == 'p')) && (local_28._1_1_ == '1')))) &&
           (((local_30._3_1_ == '3' && (local_38._1_1_ == 'n')) &&
            (((local_48._4_1_ == 'b' && (((char)local_28 == '4' && (local_40._1_1_ == 'n')))) &&
             ((char)local_38 == ',')))))))) &&
         ((((((((char)local_40 == '3' && (local_48._6_1_ == '0')) && (local_38._7_1_ == 't')) &&
             ((local_40._7_1_ == 't' && ((char)local_30 == '0')))) &&
            ((local_40._6_1_ == 'r' && ((local_28._5_1_ == '}' && (local_38._5_1_ == 'r')))))) &&
           (local_38._6_1_ == '_')) && ((local_38._2_1_ == '3' && (local_30._4_1_ == '_')))))) {
        puts("Yes! That\'s right!");
      }
      else {
        puts("No... not that");
      }
      return 0;
    }

From this code we can understand that chars get reassembled following the order of the undefined8 locals to form the flag. It starts at ((char)local_48 == 'H') and local_28._5_1_ == '}’ is where it ends, so after a bit of tidying we could retrieve the following string:


    HTB{br0k3n_4p4rt,n3ver_t0_b3_r3p41r3d}
    ((char)local_48 == 'H')
    (local_48._1_1_ == 'T'))
    (local_48._2_1_ == 'B'))
    (local_48._3_1_ == '{'))
    local_48._4_1_ == 'b'
    ((local_48._5_1_ == 'r'
    (local_48._6_1_ == '0'))
    (local_48._7_1_ == 'k'))
            ...


