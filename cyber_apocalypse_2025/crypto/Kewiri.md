---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Kewiri Writeup
desc: Check out our writeup for Kewiri for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: crypto
challenge: Kewiri
tags: "crypto, starry, sagemath, python"
date: 2025-03-29T00:00:00+00:00
last_modified_at: 2025-03-29T00:00:00+00:00
---


> Not Solved by Starry-Lord

This challenge was a bunch of questions around computing elliptic curve. There is already a very good write-up from the creator himself about what goes on in questions 1 to 6, but I’m writing this for question 6, because that’s where I had the most pain in the whole competition. https://github.com/hackthebox/cyber-apocalypse-2025/blob/main/crypto/Kewiri/README.md

For debugging purposes I used a listener script, but you don’t need it at all. I just needed to make sure my script was sending an answer in time or if the server was replying with “Too slow …”:

Listener Script:

    cat listener.py 
    import socket
    import threading
    
    # === CONFIGURATION ===
    LOCAL_HOST = '127.0.0.1'
    LOCAL_PORT = 13337
    REMOTE_HOST = '94.237.48.197'
    REMOTE_PORT = 54180
    
    def forward(source, destination, direction):
        try:
            while True:
                data = source.recv(4096)
                if not data:
                    break
    
                # Log all data
                decoded = data.decode(errors='ignore')
                print(f"[{direction}] {decoded}")
    
                # NEW: Save server messages to file
                if direction == "S→C":
                    with open("server_output.log", "a") as f:
                        f.write(decoded)
    
                destination.sendall(data)
        except Exception as e:
            print(f"[!] Connection error in {direction}: {e}")
        finally:
            source.close()
            destination.close()
    
    
    def start_proxy():
        print(f"[*] Starting proxy on {LOCAL_HOST}:{LOCAL_PORT} → {REMOTE_HOST}:{REMOTE_PORT}")
        listener = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        listener.bind((LOCAL_HOST, LOCAL_PORT))
        listener.listen(1)
        while True:
            client_sock, addr = listener.accept()
            print(f"[+] Accepted connection from {addr}")
            remote_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            remote_sock.connect((REMOTE_HOST, REMOTE_PORT))
            # Forward in both directions
            threading.Thread(target=forward, args=(client_sock, remote_sock, "C→S")).start()
            threading.Thread(target=forward, args=(remote_sock, client_sock, "S→C")).start()
    
    start_proxy()
    

Make sure you have the latest version of sagemath by using the docker image, which makes things easier than potentially wasting a couple of hours with installing an old version of sagemath from Debian packages, then removing it, then building sagemath from source. Link to build from source if you absolutely need to: https://sagemanifolds.obspm.fr/install_ubuntu.html 

After trying everything, between making a subprocess from my python to a sage script taking arguments inside the sage docker, which was “Too slow” for the server, running the sage script for question 6 inside the sage docker, and building sage from source on another Ubuntu VM, the best way I found in the end was the following:


    docker run -it -v $PWD:/mnt --name sage_bs sagemath/sagemath "sage -pip install ecdsa pwntools; sage -python /mnt/python.py"

Because YES, sage has flags as well, and it comes with its own python…

Attack script:

    from pwn import *
    import re
    import time
    import subprocess
    from ecdsa.ellipticcurve import CurveFp, Point
    from ecdsa.numbertheory import square_root_mod_prime as sqrt_mod
    from sage.all import factor, EllipticCurve, GF, pari, log, Integer
    
    host = "host.docker.internal"
    port = 13337
    conn = remote(host, port)
    time.sleep(7)
    
    stored_p = None
    stored_a = None
    stored_b = None
    xG = None
    xA = None
    
    # Q2: Factorization of p-1 (precomputed)                                                                                                                                                                                                   
    factorization_str = (                                                                                                                                                                                                                      
        "2,2_5,1_635599,1_2533393,1_4122411947,1_175521834973,1_206740999513,"                                                                                                                                                                 
        "1_1994957217983,1_215264178543783483824207,1_10254137552818335844980930258636403,1"                                                                                                                                                   
    )                                                                                                                                                                                                                                          
    factors = [int(p) for p in factorization_str.split('_') if p for p, _ in [p.split(',')]]                                                                                                                                                   
                                                                                                                                                                                                                                               
    # Q4 and Q5 constants                                                                                                                                                                                                                      
    curve_order = "21214334341047589034959795830530169972304000967355896041112297190770972306665257150126981587914335537556050020788061"                                                                                                       
    curve_order_factorization_str = (                                                                                                                                                                                                          
        "2,2_7,2_" + curve_order + ",1_2296163171090566549378609985715193912396821929882292947886890025295122370435191839352044293887595879123562797851002485690372901374381417938210071827839043175382685244226599901222328480132064138736290361668527861560801378793266019,1"                                                                                                                                                                                                           
    )                                                               
    # === Smart's Attack using minimal Sage subprocess ===
    
    def get_prime(data):
        match = re.search(rb"p\s*=\s*(\d+)", data)
        return int(match.group(1)) if match else None
    
    def parse_candidate(data):
        match = re.search(rb"(\d+)\?", data)
        return int(match.group(1)) if match else None
    
    def is_generator(g, p, factors):
        for q in factors:
            if pow(g, (p - 1) // q, p) == 1:
                return False
        return True
    
    def recover_point(curve, x, a, b, p):
        rhs = (x*x*x + a*x + b) % p
        try:
            y = sqrt_mod(rhs, p)
            return [Point(curve, x, y), Point(curve, x, p - y)]
        except Exception:
            return []
    
    # === Main logic ===
    buffer = b""
    
    while True:
        try:
            chunk = conn.recv(timeout=5)
            if not chunk:
                break
            buffer += chunk
    
            if b">" not in buffer:
                continue
    
            full_prompt = buffer
            buffer = b""
            decoded = full_prompt.decode(errors="ignore")
            print(decoded)
    
            # Parse curve prime
            if stored_p is None and b"p =" in full_prompt:
                stored_p = get_prime(full_prompt)
                print(f"[*] Parsed prime p = {stored_p}")
    
            # Parse curve parameters a and b
            if b"a =" in full_prompt:
                stored_a = int(re.search(r"a\s*=\s*(\d+)", decoded).group(1))
            if b"b =" in full_prompt:
                stored_b = int(re.search(r"b\s*=\s*(\d+)", decoded).group(1))
    
            F = GF(stored_p)
    
            # Parse x(G) and x(A)
            g_match = re.search(r"G has x-coordinate:\s*(\d+)", decoded)
            a_match = re.search(r"A has x-coordinate:\s*(\d+)", decoded)
            if g_match:
                xG = int(g_match.group(1))
                print(f"[*] x(G) = {xG}")
            if a_match:
                xA = int(a_match.group(1))
                print(f"[*] x(A) = {xA}")
    
            # Q1
            if b"how many bits" in full_prompt.lower():
                conn.sendline(str(stored_p.bit_length()).encode())
    
            # Q2
            elif b"factorization of the order of the multiplicative group" in full_prompt.lower():
                conn.sendline(factorization_str.encode())
    
            # Q4
            elif b"order of the curve defined over f_p" in full_prompt.lower():
                conn.sendline(curve_order.encode())
                E = EllipticCurve(F, [stored_a, stored_b])
                sleep(3)
    
            # Q5
            elif b"factorization of the order of the elliptic curve" in full_prompt.lower():
                conn.sendline(curve_order_factorization_str.encode())
    
            # Q3
            elif b"?" in full_prompt and full_prompt.strip().endswith(b"? >") and b"value of d" not in full_prompt.lower():
                g = parse_candidate(full_prompt)
                if g is not None:
                    result = int(is_generator(g, stored_p, factors))
                    print(f"[*] Element: {g} — Generator? {bool(result)}")
                    conn.sendline(str(result).encode())
                else:
                    print("[!] Failed to parse candidate generator.")
                    conn.sendline(b"0")
    
            # Q6 — Smart's attack using Sage subprocess
            elif b"What is the value of d?" in full_prompt:
                print("[*] Solving for d using Smart's attack via Sage subprocess")
                G = E.lift_x(Integer(xG))
                A = E.lift_x(Integer(xA))
                d = log(A, G)
    
                if d is not None:
                    print(f"[✅] Found d = {d}")
                    conn.sendline(str(d).encode())
                    print(conn.recvuntil(b'}').decode())
                else:
                    conn.sendline(b"0")
    
            else:
                print("[!] Unknown or unexpected prompt.")
                print("[DEBUG] Full prompt received:\n", decoded)
                break
    
        except EOFError:
            print("[*] Connection closed by remote host.")
            break


Flag:

![](https://i.imgur.com/WiQlXbq.png)



