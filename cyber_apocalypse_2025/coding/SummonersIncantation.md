---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Summoners Incantation Writeup
desc: Check out our writeup for Summoners Incantation for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: coding
challenge: Summoners Incantation
tags: "coding, chatgpt, python"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---



> Solved by ChatGPT


    def max_energy(tokens):
        if not tokens:
            return 0
        elif len(tokens) == 1:
            return tokens[0]
        
        # Initialize variables for DP
        include = tokens[0]  # If we include the first token
        exclude = 0  # If we exclude the first token
        
        for i in range(1, len(tokens)):
            new_include = exclude + tokens[i]  # Include current token
            new_exclude = max(include, exclude)  # Exclude current token
            
            include = new_include
            exclude = new_exclude
        
        # The result is the maximum of including or excluding the last token
        return max(include, exclude)
    # Get input from the user
    user_input = input()
    # Convert the string input into a list using eval
    try:
        tokens = eval(user_input)
        if isinstance(tokens, list) and all(isinstance(i, int) for i in tokens):
            # Process the list to find the maximum energy
            print(max_energy(tokens))
        else:
            print("Please enter a valid list of integers.")
    except:
        print("Invalid input. Please enter a valid Python-style list of integers.")


![](https://i.imgur.com/5fmRYuB.png)


