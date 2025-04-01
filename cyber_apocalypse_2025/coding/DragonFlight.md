---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Dragon Flight Writeup
desc: Check out our writeup for Dragon Flight for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: coding
challenge: Dragon Flight
tags: "coding, twh, python"
date: 2025-03-29T00:00:00+00:00
last_modified_at: 2025-03-29T00:00:00+00:00
---



> Solved by thewhiteh4t


- This one looked difficult but was actually simple once I started solving
- The Largest sum contiguous sub-array is a common programming problem


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

