# warmup

## QRRRRRRRR
> Solved by : thewhiteh4t


- A long QR code
- this is called rMQR code
- https://www.qrcode.com/en/codes/rmqr.html
- this can be read using `scandit` app available on android playstore

```
flag{a44557e380e3baae9c21c738664c6142}
```

----------


## That’s not my base
> Solved by : thewhiteh4t


- encoded string :

```
F#S<YRXdP0Fd=,%J4c$Ph7XV(gF/*]%C4B<qlH+%3xGHo)\
```

- It is base92:

```
flag{784454a9509196a33dba242c423c057a}
```

----------


## copypasta
> Solved by : thewhiteh4t


- The flag is hidden in the text printed by the challenge
- simply copy paste the text in the terminal and we get the flag


![](https://i.imgur.com/jTtMgFl.png)


```
flag{1f68e019b29650f6e8ea15a7808f76fd}
```

----------


## Twine


- An image is provided
- flag is in the strings of the image


![](https://i.imgur.com/dLpZ9r6.png)


```
flag{4ac54e3ba5f8f09049f3ad62403abb25}
```

----------


## eicar
> Solved by : thewhiteh4t

```
> md5sum eicar
44d88612fea8a8f36de82e1278abb02f  eicar
```

```
flag{44d88612fea8a8f36de82e1278abb02f}
```

----------


## Read the rules
> Solved by : thewhiteh4t


- flag is present in HTML comment


![](https://i.imgur.com/3SpYOTv.png)


```
flag{90bc54705794a62015369fd8e86e557b}
```

----------


## **Uriel**
> Solved by: Legend


- We are given a string which is encoded. And hint is also given that it is encoded twice.

```
%25%36%36%25%36%63%25%36%31%25%36%37%25%37%62%25%33%38%25%36%35%25%36%36%25%36%35%25%36%32%25%33%36%25%33%36%25%36%31%25%33%37%25%33%31%25%33%39%25%36%32%25%33%37%25%33%35%25%36%31%25%33%34%25%36%32%25%33%37%25%36%33%25%33%36%25%33%33%25%33%34%25%36%34%25%33%38%25%33%38%25%33%35%25%33%37%25%33%38%25%33%38%25%36%34%25%36%36%25%36%33%25%37%64
```

- This is an URL encoded string which gives the flag after decoding it twice.

```
flag{8efeb66a719b75a4b7c634d885788dfc}
```
