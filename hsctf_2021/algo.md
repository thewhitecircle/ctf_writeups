# algo

## not-really-math
> Solved by: nsa-bot and taz34

```
nc not-really-math.hsc.tf 1337
```

I did this manually by passing the values we get through this script one by one.

```python
import math
from pwn import *
question = input("Enter Here: ")
question = question.replace('m', '*')
question = question.replace('a', '+')
question = question.split('*')
problem = []
for i in question:
    problem.append(eval(i))
answer = math.prod(problem)
if answer > 99999:
    print(answer%4294967295)
else:
    print(answer)
```

**This is just the concept** for understanding, we can solve it using this by passing each value we get through this and the copying back the answer we get from here to the script, but that is very time consuming.

**nsa-bot** wrote an awesome script to automate the process process:

```python
import math
from pwn import *
from re import search
r = remote('not-really-math.hsc.tf',  1337)
    
data = r.recvuntil('\n: ')
data = data.decode().split('\n')
question = data[1]
question = question.replace('m', '*')
question = question.replace('a', '+')
question = question.split('*')
problem = []
for i in question:
    problem.append(eval(i))
answer = math.prod(problem)
r.sendline(str(answer))
    
flagfound = False
while flagfound == False:
    try:
        data = r.recvuntil('\n: ')
    except EOFError:
        data = r.recv()
    data = data.decode().split('\n')
    if search('flag', str(data)):
        flagfound = True
        flag = data[0]
        print('flag is ', flag)    
    else:
        question = data[0]
        question = question.replace('m', '*')
        question = question.replace('a', '+')
        question = question.split('*')
        problem = []
        for i in question:
            problem.append(eval(i))
        print('[+]Solving Question')
        answer = math.prod(problem)
        answer = answer%4294967295
        r.sendline(str(answer))
        print('[+]Sending Answer')
        flagfound = False
```

![](https://i.imgur.com/Id6cKFI.png)

```
flag{yknow_wh4t_3ls3_is_n0t_real1y_math?_c00l_m4th_games.com}
```