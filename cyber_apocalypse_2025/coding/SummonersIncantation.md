---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Summoners Incantation Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: coding
challenge: Summoners Incantation
tags: ""
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>


## Summoners Incantation
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


