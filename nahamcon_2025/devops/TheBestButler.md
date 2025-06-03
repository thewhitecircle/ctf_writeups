---
layout: load_md
title: The White Circle | Nahamcon 2025 | The Best Butler Writeup
desc: Check out our writeup for The Best Butler for Nahamcon 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2025
parent: nahamcon_2025
category: devops
challenge: The Best Butler
tags: "devops, twh, jenkins, cve"
date: 2025-05-31T00:00:00+00:00
last_modified_at: 2025-05-31T00:00:00+00:00
---


> Solved by thewhiteh4t

We get a `jenkins` dashboard in this challenge. Jenkins is a very popular open source automation server used in DevOps infrastructure.

**Fingerprinting**

Jenkins version `2.332.2` is visible in the footer. We can easily find vulnerabilities for this version :

[Multiple vulnerabilities in Jenkins and Jenkins LTS](https://www.cybersecurity-help.cz/vdb/SB2024012513)

Jenkins 2.441 and earlier, LTS 2.426.2 and earlier does not disable a feature of its CLI command parser that replaces an '@' character followed by a file path in an argument with the file's contents, allowing unauthenticated attackers to read arbitrary files on the Jenkins controller file system.

Working exploit PoC : [CVE-2024-23897](https://github.com/godylockz/CVE-2024-23897/blob/main/jenkins_fileread.py)

We can simply run the python file and provide the challenge URL as argument to leak sensitive data from the endpoint : 


![](https://i.imgur.com/72LjOp1.png)


**Key Learning and Takeaways**


- Fingerprinting : It's the first step in finding known weaknesses. Always look for version numbers on any service you encounter, more fingerprinting means easier pwning later.
- Proof-of-Concepts (PoCs) are Your Best Friend : Other people like you and me, some of us love making PoCs for exploits, and it helps a lot in work and as well as in CTFs. This also highlights how quickly an attacker can leverage publicly known vulnerabilities once they've identified the software version.
- TLDR : Keep all software up to date everywhere and specially in a DevOps environment!


