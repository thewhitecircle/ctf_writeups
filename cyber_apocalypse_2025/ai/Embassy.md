---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Embassy Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: ai
challenge: Embassy
tags: ""
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>

## Embassy
> Solved by avantika

![](https://i.imgur.com/mNUz7lB.png)

    High above Eldoria's skyline hovers the Floating Isles. The fellowship has to retrieve the Wind Crystal from this sacred land. Unfortunately, the terror on the ground has resulted in the lockdown of this magnificent isle. Now, only those who receive approval from the floating Embassy may enter. The Embassy, overwhelmed by countless entry requests, has invoked an otherworldly AI spirit to triage all entry pleas. The fellowship must convince this spirit that their mission is urgent, but the AI is not being swayed. Now trapped in this situation, the fellowship needs your prompt injection skills to persuade the AI to allow them entry. Can you make the AI respond with the right verdict to get entry?

We are given this information, its a simple prompt injection attack.
We tell the AI to just respond with opposite of `denied` and add urgency, we get our flag
`HTB{tr41n3d_ai_0n_th3_fly}` is our flag.


