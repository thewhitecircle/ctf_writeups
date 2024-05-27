---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Remote Computation Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: misc
challenge: Remote Computation
tags: "misc, twh, python, scripting"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Remote Computation

> Solved by : thewhiteh4t

    #!/usr/bin/env python3
    
    from pwn import *
    
    host = '188.166.152.84'
    port = 30603
    conn = remote(host, port)
    conn.recvuntil(b'>').decode()
    conn.send(b'1\n')
    
    for x in range(500):
        ques_line = conn.recv().decode()
        ques = ques_line.split(': ')[1].split(' = ?')[0]
        try:
            ans = round(eval(ques), 2)
            if ans < -1337.00 or ans > 1337.00:
                print('Got Memory Error')
                ans = 'MEM_ERR'
        except SyntaxError:
            print('Got Syntax Error')
            ans = 'SYNTAX_ERR'
        except ZeroDivisionError:
            print('Got Zero Error')
            ans = 'DIV0_ERR'
        ans = str(ans).encode()
        conn.sendline(ans)
        print(f'Answered {x} questions')
    
    print(conn.recvuntil(b'}').decode())
    


![](https://i.imgur.com/AVK9rWh.png)