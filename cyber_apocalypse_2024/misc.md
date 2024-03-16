# misc

## Character
> Solved by : Legend

Challenge description:

```
Security through Induced Boredom is a personal favourite approach of mine. Not as exciting as something like The Fray, but I love making it as tedious as possible to see my secrets, so you can only get one character at a time!
```

In this challenge the server is giving us the flag if we put the index value. For example if I put `0` then it will print the value `H` and so on. We need to do this till we get the complete flag till `}` character.

```python
    #! /usr/bin/python3
    
    from pwn import *
    
    SERVER_IP = "94.237.53.53"
    SERVER_PORT = 47157
    
    conn = remote(SERVER_IP, SERVER_PORT)
    
    flag = ""
    
    for index in range(111):
        conn.sendline(str(index).encode('ascii'))
    
        server_response = conn.recvline().decode().strip()
    
        character = server_response.split()[-1]
        flag += character
    
        print(f"Index {index} : Character {character}", end="\r", flush=True)
        
        if character == "}":
            break
    
    print(f"Flag: {flag}")
    
    conn.close()
```

```
Flag: HTB{tH15_1s_4_r3aLly_l0nG_fL4g_i_h0p3_f0r_y0Ur_s4k3_tH4t_y0U_sCr1pTEd_tH1s_oR_els3_iT_t0oK_qU1t3_l0ng!!}
```

---

## Stop Drop and Roll
> Solved by : Legend

Challenge description:

```
The Fray: The Video Game is one of the greatest hits of the last... well, we don't remember quite how long. Our "computers" these days can't run much more than that, and it has a tendency to get repetitive...
```

This challenge is playing a game in which we have to input based on the challenge question.
For example if it says `GORGE` we need to respond `STOP`, if `GORGE, FIRE` we need to send `STOP-ROLL` and so on till we finally get the flag.


```python
    #! /usr/bin/python3
    from pwn import *
    
    IP_ADDRESS = '83.136.252.248'
    PORT = 54677
    
    conn = remote(IP_ADDRESS, PORT)
    
    print(conn.recvuntil(b'(y/n)').decode())
    
    conn.sendline(b'y')
    
    print(conn.recvline().decode())
    
    responses = {
        'GORGE': 'STOP',
        'PHREAK': 'DROP',
        'FIRE': 'ROLL'
    }
    
    while True:
        prompt = conn.recvline().decode().strip()
        print(prompt)
    
        if "What do you do?" in prompt:
            keywords = prompt.split("What do you do?")[1].strip().split(', ')
        else:
            keywords = prompt.split(', ')
        
        response_keywords = [responses.get(keyword) for keyword in keywords]
    
        if None in response_keywords:
            break
    
        response = '-'.join(response_keywords)
        
        conn.sendline(response.encode())
        print(f"Response: {response}")
        
        if 'Unfortunate! You died!' in prompt:
            break
    
        if 'Fantastic work! The flag is' in prompt:
            flag = prompt.split('Fantastic work! The flag is')[1].strip()
            print(f"The flag is: {flag}")
            break
    
    conn.close()
```

```
The flag is: HTB{1_wiLl_sT0p_dR0p_4nD_r0Ll_mY_w4Y_oUt!}
```

---

## Unbreakable
> Solved by : thewhiteh4t

- This is a python jail challenge, at first it looks difficult because even built-ins cannot be used but then we realized two things :
    - it is using `eval` instead of `exec`
    - open() is not blocked
- Payload :

```
print(open('flag.txt').read())
```

![](https://i.imgur.com/gEM0du3.png)