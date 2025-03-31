---
layout: load_md
title: The White Circle | Hacktivitycon 2021 | WORD CHURCH Writeup
desc: Check out our writeup for WORD CHURCH for Hacktivitycon 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hacktivitycon 2021
parent: hacktivitycon_2021
category: misc
challenge: WORD CHURCH
tags: "misc, choco, python, scripting"
date: 2021-09-20T00:00:00+00:00
last_modified_at: 2021-09-20T00:00:00+00:00
---


> Solved By : choco

the whole program is about solving 30 crossword puzzles with unspecified words to find.
We have to use a script to find the words and a separate script to execute and get inputs of the crossword and words then output that position to the script

The program might encounter forkbombs or slow down but it works after a while

the crossword script :


    import re
    from itertools import islice
    
    def find_in_list_of_list(mylist, char):
        for sub_list in mylist:
            if char in sub_list:
                return (mylist.index(sub_list), sub_list.index(char))
        raise ValueError("'{char}' is not in list".format(char = char))
    
    def topright(chara,lis,i,j):
            st = "["
            for k in range(len(chara)):
                    if( (i > 15) or (j > 15)):
                            return "nope"
                    if(chara\[k]==lis[i\][j]):
                            st = st + "(" + str(j)+", "+str(i) + "), "
                            i-=1
                            j+=1
                            print(str(i) +" "+str(j))
                    else:
                            return "nope"
            st =st + "]"
            return st
    
    def right(chara,lis,i,j):
            st = "["
            for k in range(len(chara)):
                    if( (i > 15) or (j > 15)):
                            return "nope"
                    if(chara\[k]==lis[i\][j]):
                            st = st + "(" + str(j)+", "+str(i) + "), "
                            j+=1
                            print(str(i) +" "+str(j))
                    else:
                            return "nope"
            st =st + "]"
            return st
            
    def bottomright(chara,lis,i,j):
            st = "["
            for k in range(len(chara)):
                    if( (i > 15) or (j > 15)):
                            return "nope"
                    if(chara\[k]==lis[i\][j]):
                            st = st + "(" + str(j)+", "+str(i) + "), "
                            j+=1
                            i+=1
                            print(str(i) +" "+str(j))
                    else:
                            return "nope"
            st =st + "]"
            return st
            
    def top(chara,lis,i,j):
            st = "["
            for k in range(len(chara)):
                    if( (i > 15) or (j > 15)):
                            return "nope"
                    if(chara\[k]==lis[i\][j]):
                            st = st + "(" + str(j)+", "+str(i) + "), "
                            i-=1
                            print(str(i) +" "+str(j))
                    else:
                            return "nope"
            st =st + "]"
            return st
            
    def bottom(chara,lis,i,j):
            st = "["
            for k in range(len(chara)):
                    if( (i > 15) or (j > 15)):
                            return "nope"
                    if(chara\[k]==lis[i\][j]):
                            st = st + "(" + str(j)+", "+str(i) + "), "
                            i+=1
                            print(str(i) +" "+str(j))
                    else:
                            return "nope"
            st =st + "]"
            return st
    
    
    def topleft(chara,lis,i,j):
            st = "["
            for k in range(len(chara)):
                    if( (i > 15) or (j > 15)):
                            return "nope"
                    if(chara\[k]==lis[i\][j]):
                            st = st + "(" + str(j)+", "+str(i) + "), "
                            i-=1
                            j-=1
                            print(str(i) +" "+str(j))
                    else:
                            return "nope"
            st = st + "]"
            return st
    
    
    def left(chara,lis,i,j):
            st = "["
            for k in range(len(chara)):
                    if((i > 15) or (j > 15)):
                            return "nope"        
                    if(chara\[k]==lis[i\][j]):
                            st = st + "(" + str(j)+", "+str(i) + "), "
                            j-=1
                            print(str(i) +" "+str(j))
                    else:
                            return "nope"
            st =st + "]"
            return st
            
    
    def bottomleft(chara,lis,i,j):
            st = "["
            for k in range(len(chara)):
                    if((i > 15) or (j > 15)):
                            return "nope"
                    if(chara\[k]==lis[i\][j]):
                            st = st + "(" + str(j)+", "+str(i) + "), "
                            j-=1
                            i+=1
                            print(str(i) +" "+str(j))
                    else:
                            return "nope"
            st =st + "]"
            return st
            
    def find(chara,lis,i,j):
            st = ""
            if(chara\[1] == lis[(i-1)%15\][(j+1)%15]):
                    st = topright(chara,lis,i,j)
                    if(st != "nope"):
                            return st
            if(chara\[1] == lis[i\][(j+1)%15]):
                    st = right(chara,lis,i,j)
                    if(st != "nope"):
                            return st
            if(chara\[1] == lis[(i+1)%15\][(j+1)%15]):
                    st = bottomright(chara,lis,i,j)
                    if(st != "nope"):
                            return st
            if(chara\[1] == lis[(i-1)%15\][j]):
                    st = top(chara,lis,i,j)
                    if(st != "nope"):
                            return st
            if(chara\[1] == lis[(i+1)%15\][j]):
                    st = bottom(chara,lis,i,j)
                    if(st != "nope"):
                            return st
            if(chara\[1] == lis[(i-1)%15\][(j-1)%15]):
                    st = topleft(chara,lis,i,j)
                    if(st != "nope"):
                            return st
            if(chara\[1] == lis[i\][(j-1)%15]):
                    st = left(chara,lis,i,j)
                    if(st != "nope"):
                            return st
            if(chara\[1] == lis[(i+1)%15\][(j-1)%15]):
                    st = bottomleft(chara,lis,i,j)
                    if(st != "nope"):
                            return st
            return "nope"
                    
    def maind(arg0,arg1):                
            content = arg1
            #l = list(re.sub(r'[^a-zA-Z]', '', content))
            #l1 = [l[x:x+16] for x in range(0, len(l), 16)]
            l1 = arg1
            strr = arg0
            for i in range(len(l1)):
                    for j in range(len(l1[i])):
                            if(strr\[0]==l1[i\][j]):
                                    print("found in " +str(i) +" "+str(j))
                                    ans = find(strr,l1,i,j)  
                                    if(ans!="nope"):
                                            ans1 = ans[0:(len(ans)-3)]+"]"
                                            return ans1
            return " "
    if __name__ == "__main__":
            maind(sys.argv[1], sys.argv[2], sys.argv[3])

The execution script:


    import crossword
    
    from pwn import *
    
    host = 'challenge.ctf.games'
    port = 30567
    counter = 0
    r = remote(host, port)
    
    if __name__ == "__main__":
            data = r.recvuntil('> ')
            r.send(b'play')
            data = r.recvuntil('> ')
            wor = data.decode('ascii')
            l3 = []
            print(wor[20])
            while True:
                    try:
                            if wor != None and '0' in wor:
                                    s2 = "0  |"
                                    l = list(re.sub(r'[^a-zA-Z]', '', wor[wor.index(s2) + len(s2):]))
                                    l2 = [l[x:x+16] for x in range(0, len(l), 16)]
                                    l3 = l2[:len(l2)-1]
                                    print(l3)
                                    str1 = ""
                                    for ele in l2\[len(l2)-1\][1:]: 
                                            str1 += ele
                                    print(str1)
                                    ans = crossword.maind(str1,l3)
                                    print(ans)
                                    r.sendline(ans)
                                    data = r.recvuntil('> ')
                                    wor = data.decode('ascii')
                                    print(wor)
                            else:
                                    stre = re.sub(r'[^a-zA-Z]', '', wor)
                                    print(stre)
                                    ans = crossword.maind(stre,l3)
                                    print(ans)
                                    r.sendline(ans)
                                    data = r.recvuntil('> ')
                                    print(data)
                                    wor = data.decode('ascii')
                                    print(wor)
                    
                    except EOFError:
                            print(r.recv(4096).decode())
                            exit()


![](https://i.imgur.com/SsWliNM.png)


`flag{ac670e1f34da9eb748b3f241eb03f51b}`
  

