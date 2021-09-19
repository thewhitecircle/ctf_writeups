# warmups

## Target Practice
> Solved By : ava

We are given a GIF file, which actually works, and after opening we can see it has some sort of code on it, which changes pretty quick, so we need to split GIF into frames, I used https://ezgif.com/split to split and then downloaded all frames, and did a quick google re-image search on one of them, which result us in knowing that it is called MAXICODE, used by UPS.
then I found i MAXICODE decoder - https://products.aspose.app/barcode/recognize/maxicode#
and i had to manually check every image, which seems like not the intended way, but okie.
The 15th frame (if you started counting from 0) has the flag

`flag{385e3ae5d7b2ca2510be8ef4}`


----------


## 2EZ
> Solved By : thewhiteh4t


- We are given a file named `2ez`
- the file format is not known when I tested it with `file` command


![](https://i.imgur.com/v39kj3k.png)

- next I tried `binwalk` to look for any hidden files but the output was blank
- next I checked the MAGIC of the file i.e. the header
- file magic is responsible for the correct file format
- file command checks magic and file footer to determine correct file type


![](https://i.imgur.com/SmobPBK.png)



- JFIF header means a jpeg file
- correct header for JFIF in hex is : `FF D8 FF`
- but if we look at the file given to us its different, so I fixed it using a hex editor


![](https://i.imgur.com/vPgfam0.png)

- Saved it as a new file and solved


![](https://i.imgur.com/rIBarpI.png)

----------


## TSUNAMI 
> Solved by: Taz34


- We are given a .wav audio file
- at the end of the audio we can here sum disturbances 
- so i checked the audio file with sonic visualizer 
- checked with spectrograms
![](https://i.imgur.com/QSmfYA2.jpg)


 here is the flag.
 

----------


## Six Four Over Two
> Solved by: Taz34

we have a cipher text give, run it through cyber chef and decode it to get the flag.

![](https://i.imgur.com/tyObCAb.png)



----------


## Pimple
> Solved By : thewhiteh4t


- We are given a gimp project file in this challenge
- there are multiple layers
- each layer contains an image
- to see the flag I started hiding the layers one by one from top and eventually saw the flag


![](https://i.imgur.com/JTQSPdT.png)



----------


## Bass64
> Solved By : thewhiteh4t


- We are given a text file in this challenge
- file contains letters and numbers in ASCII art
- it’s actually a base64 string
- converting it gives the flag


![](https://i.imgur.com/IlcrRzA.png)



    flag{35a5d13da6a2afa0c62bfcbdd6301a0a}


----------


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

