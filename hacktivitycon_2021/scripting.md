# scripting

## UHAHA
> Solved By : ava and thewhiteh4t

We are given a file called uhaha, upon checking the file using the `file` command, we come to know that it is type of archive called UHARC, fairly rare one that is. Obvious conclusion seems like extracting it but oh, look it is password protected, and the description of challenge did mention we might need to use rockyou, so it looks like it is time for bruteforcing. Looks like we are going to need a terminal tool and write a script for bruteforcing, apparently we couldn’t find a linux tool to extract UHARC archive, running it under wine was a possibility but that would just complicate the whole thing, so we got a windows tool for it linked here - https://sam.gleske.net/uharc/
just installing it and locating `uharc.exe` will do the job
and it looks like it is archive inside a archive….archiveception!
So the following script extracts it repeatedly until we get flag.


    from os import rename, remove
    import subprocess as subp
    filename = 'uhaha.uha'
    wordlist_path = 'rockyou.txt'
    def extract(filename):
        counter = 0
        with open(wordlist_path, 'r') as wordlist:
            for line in wordlist:
                counter += 1
                word = line.strip()
                #print(f'Trying {word}')
                proc = subp.Popen(['uharc.exe', 'e' , f'-pw{word}', filename], stdout=subp.PIPE)
                subp.call(['taskkill', '/F', '/PID', str(proc.pid)], stdout=subp.PIPE, stderr=subp.PIPE)
                stdout = proc.stdout.read()
                output = stdout.decode()
                if 'ERROR' in output:
                    pass
                elif 'successfully' in output:
                    print(f'PASSWORD : {word}')
                    break
                else:
                    print('Unknown error occured, weird tool...')
                    print(output)
                    break
                if counter > 200:
                    break
    
    extract(filename)
    while True:
        try:
            rename('uhaha', 'uhaha2.uha')
        except FileNotFoundError:
            print('Extracted -> flag.png')
            exit()
        extract('uhaha2.uha')
        remove('uhaha2.uha')


![](https://i.imgur.com/7tmo3ET.png)


