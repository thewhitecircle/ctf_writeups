# coding

## Enchanted Cipher
> Solved by  avantika, thewhiteh4t


- Each entry has been encoded with a shifting cipher that changes every N characters

```
    import ast
    import string
    
    inputStr = input()
    groups = int(input())
    shifts = list(ast.literal_eval(input()))
    
    alphabets = list(string.ascii_lowercase)
    
    space_ind = []
    i = 0
    
    for letter in inputStr:
        i += 1
        if letter == ' ':
            space_ind.append(i)
    
    string = inputStr.replace(" ", "")
    
    groups = [string[i:i + 5] for i in range(0, len(string), 5)]
    
    flag = []
    for shift, word in zip(shifts, groups):
        output = ''
        for char in word:
            char_index = alphabets.index(char) + 1
            new_char_ind = char_index - shift
            if new_char_ind < 0:
                new_char_ind = 26 + new_char_ind
            new_char = alphabets[new_char_ind - 1]
            output += new_char
        flag.append(output)
    
    flag_pre = "".join(flag)
    
    j = 0
    final_flag = ''
    
    for space in space_ind:
        for flag_char in flag_pre:
            j += 1
            if j == space:
                x = j - 1
                flag_pre = flag_pre[:x] + ' ' + flag_pre[x:]
                final_flag = flag_pre
                break
    
    print(final_flag)
```    


![](https://i.imgur.com/oy8ZeFl.png)

----------


## Dragon Fury
> Solved by thewhiteh4t


- Simulate the battle by computing the total damage dealt over successive rounds until victory is achieved

```
    import json
    import random
    
    input_text = json.loads(input())
    T = int(input())
    num_sublists = len(input_text)
    
    def magic():
        rand_t = 0
        choices = []
        for sublist in input_text:
            rand = random.choice(sublist)
            choices.append(rand)
            rand_t += rand
        return rand_t, choices
    
    random_sum = 0
    flag_choices = None
    
    while random_sum != T:
        random_sum, flag_choices = magic()
    
    print(flag_choices)
```

![](https://i.imgur.com/2po9B6B.png)

----------


## Dragon Flight
> Solved by thewhiteh4t


- This one looked difficult but was actually simple once I started solving
- Largest sum contiguous sub-array is a common programming problem


https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/



    input1 = input().split(' ')
    num_ops = int(input1[1])
    other_inputs = []
    for i in range(num_ops + 1):
        other_inputs.append(input().split(' '))
    input2 = other_inputs[0]
    ops_list = other_inputs[1:]
    # https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/
    def maxSubarraySum(arr):
        res = arr[0]
        maxEnding = arr[0]
        for i in range(1, len(arr)):
            maxEnding = max(maxEnding + arr[i], arr[i])
            res = max(res, maxEnding)
            
        return res
    for op in ops_list:
        if 'Q' in op:
            l_side = int(op[1]) - 1
            r_side = int(op[2])
            seg_range = input2[l_side:r_side]
            seg_range = list(map(int, seg_range))
            print(maxSubarraySum(seg_range))
        elif 'U' in op:
            effect_ind = int(op[1])
            new_val = int(op[2])
            input2[effect_ind - 1] = new_val
    


![](https://i.imgur.com/BeI04WV.png)

----------
## The Clockwork Guardian
> Solved by ChatGPT


- The challenge expected us to move from one corner to another in 2D array
- I used ChatGPT for this one, obviously :)

```
    from collections import deque
    import ast
    
    def shortest_safe_path(grid):
        rows, cols = len(grid), len(grid[0])
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]  # Up, Down, Left, Right
    
        # Find the start (0,0) and exit ('E') position
        start = (0, 0)
        
        # BFS Initialization
        queue = deque([(start[0], start[1], 0)])  # (row, col, distance)
        visited = set([start])
        
        while queue:
            r, c, dist = queue.popleft()
            
            # Check if we reached the exit
            if grid\[r\][c] == 'E':
                return dist
            
            # Explore all possible moves
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                
                # Check valid move
                if 0 <= nr < rows and 0 <= nc < cols and (nr, nc) not in visited:
                    if grid\[nr\][nc] == 0 or grid\[nr\][nc] == 'E':  # Safe to move
                        queue.append((nr, nc, dist + 1))
                        visited.add((nr, nc))
        
        return -1  # No valid path found
    
    # Find shortest safe path
    grid = ast.literal_eval(input())
    result = shortest_safe_path(grid)
    print(result)
```

![](https://i.imgur.com/TQgOAkV.png)

----------


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


