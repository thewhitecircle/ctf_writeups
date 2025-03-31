---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Pursue the tracks Writeup
desc: Check out our writeup for Pursue the tracks for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: forensics
challenge: Pursue the tracks
tags: "forensics, twh, mft, analyzemft, mftexplorer, mftecmd"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by : thewhiteh4t

```
To get the flag, you need to answer the questions from the docker instance.
```


- We get a `.mft` file in this challenge and we need to answer questions based on that
- Tools used :
```
analyzeMFT.py - https://github.com/dkovar/analyzeMFT
MFTExplorer - https://www.sans.org/tools/mftexplorer/
MFTECmd - https://github.com/EricZimmerman/MFTECmd
```
- First we extracted the data in CSV format on linux : 
    - `analyzeMFT.py -f z.mft -o result.csv`


![](https://i.imgur.com/oYrBlKt.png)

- with some grep-cut we can see a list of files :


![](https://i.imgur.com/XOSWO0p.png)

- Some pointers which are important for solving :
    - analyzeMFT.py shows limited information so for things like Q6 its easily visible in MFTExplorer
    - Modified after creation in Q7 is also visible in MFTExplorer
    - Accurate file size in Q9 is not shown by either analyzeMFT or MFTExplorer for that we had to use MFTECmd
- Answers of all the questions : 

```
Q1. Files are related to two years, which are those?
> 2023,2024

Q2. There are some documents, which is the name of the first file written?
> Final_Annual_Report.xlsx

Q3. Which file was deleted?
> Marketing_Plan.xlsx

Q4. How many of them have been set in Hidden mode?
> 1

Q5. Which is the filename of the important TXT file that was created?
> credentials.txt

Q6. A file was also copied, which is the new filename?
> Financial_Statement_draft.xlsx

Q7. Which file was modified after creation?
> Project_Proposal.pdf

Q8. What is the name of the file located at record number 45?
> Annual_Report.xlsx

Q9. What is the size of the file located at record number 40?
> 57344
```

![](https://i.imgur.com/9wycXOw.png)


