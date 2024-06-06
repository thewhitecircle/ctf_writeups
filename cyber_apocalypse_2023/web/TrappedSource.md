---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Trapped Source Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: web
challenge: Trapped Source
tags: "web, starry, js"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

## Trapped Source
> Solved by Starry-Lord

![challenge description](https://i.imgur.com/QhaEep8.png)


When we open the devtools and check the network tab, we can already catch a script.js:

![network tab](https://i.imgur.com/hV9DZ4t.png)


script.js content:


    currentPin = []
    
    const checkPin = () => {
            pin = currentPin.join('')
    
            if (CONFIG.correctPin == pin) {
                    fetch('/flag', {
                            method: 'POST',
                            headers: {
                                    'Content-Type': 'application/json'
                            },
                            body: JSON.stringify({
                                    'pin': CONFIG.correctPin
                            })
                    })
                    .then((data) => data.json())
                    .then((res) => {
                            $('.lockStatus').css('font-size', '8px')
                            $('.lockStatus').text(res.message)
                    })
                    return
            }
    
            $('.lockStatus').text('INVALID!')
            setTimeout(() => {
                    reset()
            }, 3000)
    
    }
    
    const unlock = (pin) => {
            currentPin.push(pin)
    
            if (currentPin.length > 4) return
    
            $('.lockStatus').text(currentPin.join(' '))
    }
    
    const reset = () => {
            currentPin.length = 0
            $('.lockStatus').css('font-size', 'x-large')
    
            $('.lockStatus').text('LOCKED')
    }

We can see some code that checks for a 4 digit pin and fetches the flag, depending on the pin being equal to the value of “CONFIG.correctPin” or not. Switching to the console, we can try and log the “CONFIG”, to see what comes back:


![get variables](https://i.imgur.com/0NPqIRJ.png)


Typing the correct pin and pressing enter returns our flag:


![flag for this challenge](https://i.imgur.com/YmTsLrt.png)