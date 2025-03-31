---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | The Clockwork Guardian Writeup
desc: Check out our writeup for The Clockwork Guardian for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: coding
challenge: The Clockwork Guardian
tags: "coding, chatgpt, python"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

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

