# web

## Never gonna tell a lie and type you
> Solved by : thewhiteh4t


- we are given a simple PHP site which contains a single page
- errors are enabled so that helps in debugging
- first the server expects a specific user agent :

```php
if ($_SERVER['HTTP_USER_AGENT'] != "friendlyHuman"){
    die("we don't tolerate toxicity");
}
```

- we can easily change the `User-Agent` header to `friendlyhuman`
- second the server expects POST request with "data" key

```php
$user_input = json_decode($_POST["data"]); 
```

- to satisfy this we will add the following in headers :

```
Content-Type: application/x-www-form-urlencoded
.
.
.
data={}
```

- next the server expects a specific username, a password in the form of a number and a command :

```php
if($user_input->{'user'} === "admin🤠") {
        if ($user_input->{'password'} == securePassword($user_input->{'password'})  ){
            echo " hail admin what can I get you ". system($user_input->{"command"});
        }
```

- as we can see the password is compared with the result of `securePassword` function and the command is passed to a system call
- and the vulnerability lies in the double comparison : `==`
- now let's look at the password function :

```php
function securePassword($user_secret){
    if ($user_secret < 10000){
        die("nope don't cheat");
    }
    $o = (integer) (substr(hexdec(md5(strval($user_secret))),0,7)*123981337);
    return $user_secret * $o ;
}
```

- function expects input as INT and it should not be less that 10K
- then it uses `strval` to convert input to string which is passed to `md5` which calculates an md5sum which is then passed to `hexdec` which converts to a hexadecimal string and then finally it is passed to `substr` which takes first 7 characters of the result
- the result is multiplied by another large number and the result of that is finally multiplied by our password input
- in PHP when we pass a very large number which is beyond its maximum limit it converts it to `float(INF)` i.e. infinite
- when we pass it the password function the result is also of the form INF and the condition satisfies :


![](https://i.imgur.com/smEhWDz.png)

----------


## todo
> Solved by : thewhiteh4t


- when we input html into admin form input then the following flow is executed in back-end :

```
/admin -> set flag cookie -> visit site -> fill our input in /chal input -> screenshot after delay
```

- flow in `/chal` route :

```
CSP is set -> script.js is added to head tag -> our input is added
```

- flow in `/script.js` route :

```
if the flag cookie matches -> fake flag is replaced with actual flag -> response is sent
```

- so the actual flag is stored inside `script.js`
- CSP :

```
default-src 'none'; script-src 'self' 'unsafe-inline';
```

- cannot load js url like `/script.js` in src because connect-src is not present so default-src fallback is used which is none so request is blocked
- thanks to unsafe-inline we can execute js scripts
- after looking into default-src bypass I found the following on hacktricks :


![](https://i.imgur.com/J1hLfFK.png)

- [Hacktricks | CSP Bypass](https://book.hacktricks.xyz/pentesting-web/content-security-policy-csp-bypass)
- so I tried to load `/script.js` in form action like this :

```html
<form action="/script.js" id="plz">
    <button type="submit"></button>
</form>
<script>document.getElementById("plz").submit();</script>
```

![](https://i.imgur.com/uQD36xS.png)


