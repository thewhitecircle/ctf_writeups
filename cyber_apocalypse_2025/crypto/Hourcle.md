---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Hourcle Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: crypto
challenge: Hourcle
tags: "crypto, xan0er, python, aes"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>


## Hourcle
> Solved by Xan0er


- In this challenge, we are given a python server script: `server.py`. Let’s analyze this script and see if we can find any weakness.

```
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad
    import os, string, random, re
    KEY = os.urandom(32)
    password = ''.join([random.choice(string.ascii_letters+string.digits) for _ in range(20)])
    def encrypt_creds(user):
        padded = pad((user + password).encode(), 16)
        IV = os.urandom(16)
        cipher = AES.new(KEY, AES.MODE_CBC, iv=IV)
        ciphertext = cipher.decrypt(padded)
        return ciphertext
    def admin_login(pwd):
        return pwd == password
    
    def show_menu():
        return input('''
    =========================================
    ||                                     ||
    ||   🏰 Eldoria's Shadow Keep 🏰       ||
    ||                                     ||
    ||  [1] Seal Your Name in the Archives ||
    ||  [2] Enter the Forbidden Sanctum    ||
    ||  [3] Depart from the Realm          ||
    ||                                     ||
    =========================================
    Choose your path, traveler :: ''')
    def main():
        while True:
            ch = show_menu()
            print()
            if ch == '1':
                username = input('[+] Speak thy name, so it may be sealed in the archives :: ')
                pattern = re.compile(r"^\w{16,}$")
                if not pattern.match(username):
                    print('[-] The ancient scribes only accept proper names-no forbidden symbols allowed.')
                    continue
                encrypted_creds = encrypt_creds(username)
                print(f'[+] Thy credentials have been sealed in the encrypted scrolls: {encrypted_creds.hex()}')
            elif ch == '2':
                pwd = input('[+] Whisper the sacred incantation to enter the Forbidden Sanctum :: ')
                if admin_login(pwd):
                    print(f"[+] The gates open before you, Keeper of Secrets! {open('flag.txt').read()}")
                    exit()
                else:
                    print('[-] You salt not pass!')
            elif ch == '3':
                print('[+] Thou turnest away from the shadows and fade into the mist...')
                exit()
            else:
                print('[-] The oracle does not understand thy words.')
    if __name__ == '__main__':
        main()
```

- First issues that ChatGPT helped me noticed is that `encrypt_creds` function uses `cipher.decrypt()` instead of `cipher.encrypt()`, which means the encryption logic is incorrect.
- The `password` is a random 20-character alphanumeric string.
- `encrypt_creds(user)` takes a username, appends the password, pads the result to 16-byte blocks, and encrypts it using **AES-CBC** with a **random IV**.


- Exploit logic:
    - Since the **AES block size is 16 bytes**, any username of **16 bytes or longer** ensures that the password starts in a new block.
    - Send a 47-character username (`'A' * 47`), ensuring the password starts in a new block.
    - This will make sure that the first character of the password is at the end of the third block and now we can proceed with the brute force attack.


- Here is the script I’ve written with the help of ChatGPT to brute force password:

```
    from pwn import *
    import string
    
    HOST = "<HOST_IP>"
    PORT = <HOST_PORT>
    CONN = remote(HOST, PORT)
    
    def fetch_scroll(payload):
        CONN.recvuntil(b"Choose your path, traveler :: ")
        CONN.sendline(b"1")
        
        CONN.recvuntil(b"[+] Speak thy name, so it may be sealed in the archives :: ")
        CONN.sendline(payload.encode())
        
        CONN.recvuntil(b"[+] Thy credentials have been sealed in the encrypted scrolls: ")
        return CONN.recvline().decode().strip()
    
    def brutef_pass():
        print("Started bruteforce ...")
        
        password = ""
        pass_len = 20
        chr_set = string.ascii_letters + string.digits
        for i in range(pass_len):
            print(f"Guessing character {i+1} ...")
            payload = "A" * (47 - len(password))
            base_ct = fetch_scroll(payload)[32:96]
            for ch_ in chr_set:
                test_input = "A" * (47 - len(password)) + password + ch_
                new_ct = fetch_scroll(test_input)[32:96]
                if new_ct == base_ct:
                    password += ch_
                    print(f"Password guessed: {password}")
                    break
        print(f"Password: {password}")
    
    brutef_pass()
    CONN.interactive()


    HTB{encrypting_with_CBC_decryption_is_as_insecure_as_ECB___they_also_both_fail_the_penguin_test_6d41782d16675bc01c36f191056fc635}
```
