---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | ODDBALL Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: warmups
challenge: ODDBALL
tags: "warmup, choco, python, scripting"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---

<h1 class="heading card-title white-text">Hacktivitycon 2021</h1>



## ODDBALL
> Solved By : choco

the given file is an octal dump


    0000000 067531 020165 067165 067543 062566 062562 020144 064164
    0000020 020145 062563 071143 072145 066440 071545 060563 062547
    0000040 035440 005051 072516 061155 071145 020163 067151 071040
    0000060 067141 062547 030040 033455 020077 066510 066555 020041
    0000100 067510 020167 062157 005041 063012 060554 075547 060462
    0000120 031065 061462 033062 034143 060467 031546 034461 061062
    0000140 031064 031543 063064 031544 033062 034063 061065 005175
    0000160

All we need to do is to convert this octal to hex dump that is split into 16 parts in each line


    def octtodec(decnum):
        i = 0
        octdecnum = 0
        while decnum != 0:
            rem = decnum % 10
            octdecnum += rem * 8**i 
            decnum = decnum // 10
            i += 1
        return octdecnum
    
    my_file = open("oddball", "r")
    content = my_file.read()
    content = content.replace('\n',' ')
    content_list = content.split(" ")
    print(content_list)
    num = []
    res = ""
    j = 0
    for i in range(len(content_list)-1):
            if(i%9!=0):
                    res1 = hex(octtodec(int(content_list[i])))
                    if(len(res1)<6):
                            res1 = res1[0:2]+'0'+ res1[2:]
                    print(res1)
                    res = res + res1[4:6] + " " + res1[2:4] + " "
                    print(res)
            else:
                    num = str(j*10)
                    j += 1        
                    res = res + "\n" + num.zfill(8) + " "
    print(num)
    #x = 9
    #res = [content_list[i:i+x] for i in range(0, len(content_list), x)]
    #print(res)

output will be this


    00000000 59 6f 75 20 75 6e 63 6f 76 65 72 65 64 20 74 68 
    00000010 65 20 73 65 63 72 65 74 20 6d 65 73 73 61 67 65 
    00000020 20 3b 29 0a 4e 75 6d 62 65 72 73 20 69 6e 20 72 
    00000030 61 6e 67 65 20 30 2d 37 3f 20 48 6d 6d 6d 21 20 
    00000040 48 6f 77 20 6f 64 21 0a 0a 66 6c 61 67 7b 32 61 
    00000050 35 32 32 63 32 36 63 38 37 61 66 33 31 39 32 62 
    00000060 34 32 63 33 34 66 64 33 32 36 33 38 35 62 7d 0a 

you’ll get the flag when converting this hexdump


    You uncovered the secret message ;)
    Numbers in range 0-7? Hmmm! How od!
    
    flag{2a522c26c87af3192b42c34fd326385b}

