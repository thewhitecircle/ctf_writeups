---
layout: load_md
title: The White Circle | Nahamcon 2025 | Cryptoclock Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: crypto
challenge: Cryptoclock
tags: "crypto, legend, xor, known plaintext, python"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---

<h1 class="heading card-title white-text">Nahamcon 2025</h1>

## Cryptoclock
> Solved by Legend

In this challenge the server is giving us the flag and encrypting the key using XOR and timestamp at which we are connecting to the server and requesting for the encrypted flag.

The crucial hint from the challenge was the server accepting user input. This is a classic "known plaintext attack" scenario. If we can get the server to encrypt a known string, we can then deduce the XOR key.

So idea was to send a string of known characters to the server, ensuring its length matches the flag's length.  The initial problem statement mentioned the key being generated from the timestamp. This means the random.seed() function on the server side was likely seeded with `time.time()`. 

Since perfect synchronization is unlikely, we can try timestamps in a small window around our estimated now. A range of `[-5, +5]` seconds is usually sufficient to account for minor clock skews and network delays, so we need to use brute force. The random module in Python, when seeded with the same integer, produces the exact same sequence of "random" bytes.

For each guess we :

- Seed the random module : `random.seed(guess_time)`
- Generate a key of the appropriate length (equal to the flag length).
- XOR this generated key with the original encrypted flag.

I used `chatgpt` to make the solution script.


    import socket
    import time
    import random
    
    def xor(data, key):
        return bytes(a ^ b for a, b in zip(data, key))
    
    def generate_key(length, seed):
        random.seed(seed)
        return bytes(random.randint(0, 255) for _ in range(length))
    
    def recover_flag(enc_flag_hex, known_plain, enc_known_hex, time_guess):
        enc_flag = bytes.fromhex(enc_flag_hex)
        enc_known = bytes.fromhex(enc_known_hex)
        
        key = xor(known_plain.encode(), enc_known)
        flag = xor(enc_flag, key)
        return flag
    
    # Connect to the challenge
    s = socket.create_connection(('challenge.nahamcon.com', 32210))  # Or remote address
    banner = s.recv(4096).decode()
    
    # Parse encrypted flag
    import re
    enc_flag_hex = re.search(r'encrypted flag is: ([0-9a-f]+)', banner).group(1)
    flag_len = len(bytes.fromhex(enc_flag_hex))
    
    # Send known plaintext
    known_plain = 'A' * flag_len
    s.send((known_plain + '\n').encode())
    enc_known_hex = s.recv(1024).decode().split('Encrypted: ')[1].strip()
    
    # Try time guesses around now
    now = int(time.time())
    for delta in range(-5, 6):  # Try from -5 to +5 seconds
        guess_time = now + delta
        random.seed(guess_time)
        key = bytes(random.randint(0, 255) for _ in range(flag_len))
        
        decrypted = xor(bytes.fromhex(enc_flag_hex), key)
        try:
            print(f"[+] Guess {guess_time}: {decrypted.decode()}")
        except:
            pass


![](https://i.imgur.com/oZg220p.png)


**Key Learning and Takeaways**

- The challenge used XOR encryption with a key generated from the current timestamp, which made it predictable and insecure.
- By sending a known input (like `'A' * length`), it was possible to recover the key stream through a simple XOR operation.
- Brute-forcing timestamps within a short range (±5 seconds) was enough to guess the correct key and decrypt the flag.
- This showed how using predictable values (like `time.time()`) in crypto can lead to serious vulnerabilities.
- Parsing server output with regex was a quick way to extract the encrypted flag, a handy technique for similar challenges.

