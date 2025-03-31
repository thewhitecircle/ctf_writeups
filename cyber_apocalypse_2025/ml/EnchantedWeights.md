---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Enchanted Weights Writeup
desc: Check out our writeup for Enchanted Weights for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: ml
challenge: Enchanted Weights
tags: "ml, starry, pytorch"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---


> Solved by Starry-Lord

![](https://i.imgur.com/No8cCK5.png)


The clue from the title was to look into weights for the downloadable file:
`eldorian_artifact.pth`

The `pth` extension indicates we are working with a serialized PyTorch model, so we could query its Keys:

check_keys.py :


    import torch
    
    # Load the .pth file
    data = torch.load("eldorian_artifact.pth", map_location="cpu")  # map_location avoids GPU issues
    
    # See what kind of object it is
    print("Type:", type(data))
    
    # If it's a dict, list the keys
    if isinstance(data, dict):
        print("Keys:", data.keys())
        
        # If it's a model state dict
        if 'model_state_dict' in data:
            print("\nModel State Dict:")
            for k, v in data['model_state_dict'].items():
                print(f"{k}: {v.shape}")
        
        # If it's a pure state_dict
        elif all(isinstance(v, torch.Tensor) for v in data.values()):
            print("\nState Dict:")
            for k, v in data.items():
                print(f"{k}: {v.shape}")

Output:


    Type: <class 'collections.OrderedDict'>
    Keys: odict_keys(['hidden.weight'])
    
    State Dict:
    hidden.weight: torch.Size([40, 40])

With this we now know the following details:

- It's a `OrderedDict`
- Only one parameter is present: `'hidden.weight'`
- Its shape is `torch.Size([40, 40])`, which suggests a single hidden layer

A bunch of testing allowed me to find out it was diagonal values, here is the final script:


    import torch
    import torch.nn as nn
    
    # Load state dict
    state_dict = torch.load("eldorian_artifact.pth", map_location="cpu")
    weights = state_dict["hidden.weight"]
    
    # Confirm it's diagonal
    print("[*] Weight Matrix Shape:", weights.shape)
    is_diagonal = torch.all(weights == torch.diag(torch.diagonal(weights)))
    print("[*] Is Diagonal:", is_diagonal)
    
    # Extract diagonal values (since only they matter)
    diag = torch.diagonal(weights)
    print("\n[*] Diagonal Values:")
    for i, val in enumerate(diag):
        try:
            ch = chr(int(val.item()))
            if ch.isprintable():
                print(f"{i:2}: {int(val.item())} → '{ch}'")
            else:
                print(f"{i:2}: {int(val.item())} → (non-printable)")
        except:
            print(f"{i:2}: {val.item()} → (invalid)")
    
    # Try reconstructing a string directly from the diagonal
    print("\n[*] Flag (raw ASCII):")
    try:
        chars = [chr(int(x.item())) for x in diag]
        print("".join(chars))
    except:
        print("[!] Could not convert all to ASCII.")

Output: 


    [*] Weight Matrix Shape: torch.Size([40, 40])
    [*] Is Diagonal: tensor(True)
    
    [*] Diagonal Values:
     0: 72 → 'H'
     1: 84 → 'T'
     2: 66 → 'B'
     3: 123 → '{'
     4: 67 → 'C'
     5: 114 → 'r'
     6: 121 → 'y'
     7: 53 → '5'
     8: 116 → 't'
     9: 52 → '4'
    10: 108 → 'l'
    11: 95 → '_'
    12: 82 → 'R'
    13: 117 → 'u'
    14: 78 → 'N'
    15: 51 → '3'
    16: 115 → 's'
    17: 95 → '_'
    18: 48 → '0'
    19: 102 → 'f'
    20: 95 → '_'
    21: 69 → 'E'
    22: 108 → 'l'
    23: 100 → 'd'
    24: 48 → '0'
    25: 114 → 'r'
    26: 49 → '1'
    27: 97 → 'a'
    28: 125 → '}'
    29: 95 → '_'
    30: 95 → '_'
    31: 95 → '_'
    32: 95 → '_'
    33: 95 → '_'
    34: 95 → '_'
    35: 95 → '_'
    36: 95 → '_'
    37: 95 → '_'
    38: 95 → '_'
    39: 95 → '_'
    
    [*] Possible flag (raw ASCII):
    HTB{Cry5t4l_RuN3s_0f_Eld0r1a}___________
    

