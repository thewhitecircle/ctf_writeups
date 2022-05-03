# misc

## To be and not to be
> Solved by : thewhiteh4t

This challenge excepts only alphabets and character length is 3

Found the solution here :

```
https://github.com/freeCodeCamp/freeCodeCamp/issues/11763
```

![](https://i.imgur.com/DJrIC15.png)

----------

## Gossip
> Solved By : Starry-Lord, Taz, Legend, thewhiteh4t

This one involved an ssh connection to a Linux machine. We could find kubernetes secrets in the usual /run/secrets/kubernetes.io/serviceaccount which revealed it was a kubernetes container.
After a bit of enumeration I learned about CVE-2022-0185 which allows us to escape the container, but it looked like it had been patched.

Further enumeration led me to find a few sticky bits on some of the binaries in /usr/bin. I looked them up to find a fitting one called dialog, which will allow us to read files with elevated permissions:

```
user@gossip-9d9e950dfdcbda12-64cdd78676-psbqk:/usr/bin$ ls -la dialog 
-rwsr-sr-x 1 root root 260736 Jan  3 23:30 dialog
```

![](https://i.imgur.com/EZjKAA0.png)

After a bit of cleaning up we can use this key to login as root.


![](https://i.imgur.com/jdEnsST.png)

----------

## Steam Locomotive
> Solved by: Legend

In this challenge we were provided with a ssh credentials to get the flag, with a hint that ls command was getting mistyped accidentally.

When I connect to the ssh it was playing an animation of steam engine, `sl` command which is Steam Locomotive, and then when the animation was over then immediately the session was getting disconnected.

![](https://i.imgur.com/zW5ZZwB.png)

To retrieve the flag we needed to read the flag and since ssh allows direct command execution during connection we can read the flag using that.

![](https://i.imgur.com/14tSoKE.png)