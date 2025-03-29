---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Hillside Haven Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: osint
challenge: Hillside Haven
tags: "osint, xan0er, maps, streetview, houses"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>


## Hillside Haven
> Solved by Xan0er


![](https://i.imgur.com/RM2BxJW.jpeg)

- We need to locate the house shown in the image above.
- Google reverse image gives us multiple hints that this house is located in the California region.
- Also, house number is visible in image: `356`
- I searched through Google reverse image results for hours but still couldn’t find this house, so I took the help of ChatGPT


- I asked ChatGPT to give me list of cities in California:
    - Los Angeles
    - San Francisco
    - San Diego
    - Sacramento
    - San Jose
    - Oakland
    - Fresno
    - Bakersfield
    - Santa Barbara
    - Palm Springs
    - Lake Tahoe
    - Monterey
    - Napa Valley


- I created a prompt to look through cities in **California**
    
    I am going to give you name of a city in California and I want you to list every possible area which might have house structure like shown in the picture. The city name is: <California_City>


- Next, I started looking through each area street views to find neighborhood where houses structure might match the one in given image


- First, I started with **Los Angeles**
    - Mt. Washington – Known for its hillside homes, older bungalows, and narrow streets.
    - El Sereno – A mix of older homes, many with driveways and front yards.
    - Highland Park – Has vintage houses, some with similar structures and driveway layouts.
    - Eagle Rock – Features older single-family homes with yards and garages.
    - Glassell Park – A mix of mid-century homes and older structures in hilly terrain.
    - Sunland-Tujunga – Suburban-like with a mix of smaller homes, large yards, and some overgrown vegetation.
    - Arleta & Panorama City – Older neighborhoods with similar house designs.
    - Sylmar – Features homes with driveways, garages, and a rustic suburban feel.
    - Mar Vista & Venice (Certain Parts) – Some older homes in these areas have driveways and detached garages.
    - Westchester (Near LAX) – Some neighborhoods have homes with similar layouts.
    - Lincoln Heights – One of L.A.’s oldest neighborhoods with a mix of small homes and hilly streets.
    - Boyle Heights (Hilly Parts) – Has older houses with detached garages and driveways.
    - Cypress Park – Smaller homes with sloped yards and driveways.
    - Montecito Heights – A mix of hillside homes, some older structures like the one in your image.
    - North Hollywood (Older Parts) – Some homes have a similar structure with garages and driveways.
    - Valley Glen – Features mid-century houses, some resembling the one in the image.
    - Reseda (Hilly Side) – A mix of old and new homes, some with overgrown yards.
    - Lake View Terrace – A more rural part of L.A. with homes that have a similar driveway and layout.


- I couldn’t find any clues in **Los Angeles**, so the next stop is **San Francisco**
    - Bernal Heights – Many homes have front-facing garages and are on sloped streets.
    - Glen Park – A mix of older homes, often built into the hillside.
    - Noe Valley – Some older homes with front garages on steep streets.
    - Potrero Hill – Similar structures, with some houses featuring small front yards or driveways.
    - Twin Peaks – Some homes have driveways leading to garages at the base, especially on winding roads.
    - Diamond Heights – A mix of mid-century and older homes with sloped lots.
    - Outer Mission/Excelsior – More single-family homes with driveways and small yards.
    - Visitacion Valley – Has some older, compact homes with driveways and garages in front.
    - Ingleside/Ingleside Heights – Houses with similar layouts and front-facing garages.
    - Parkside & Outer Sunset – While more flat, some homes have garages and driveways in front.
    - Lakeshore & Merced Heights – More space and driveways, similar to the image but slightly different layout.
    - Bayview-Hunters Point - This area has a mix of older, small single-family homes with front garages and driveways.
    - Crocker-Amazon - Located near Daly City, this area has small homes with front-facing garages and driveways.
    - Silver Terrace - Directly next to Bayview, this area has modest, older homes with garages and driveways in front.
    - Excelsior (Deeper Parts, Near McLaren Park) - Some streets have detached or semi-detached homes with driveways and front garages.
    - Outer Mission (Close to Daly City Border) - Houses here often have a single-car garage in the front, similar to the structure in your picture.


- No luck in **San Francisco** so I check **San Diego** next
    - City Heights – Older homes with smaller lots and narrow driveways.
    - North Park – Some areas have older bungalows with detached garages.
    - South Park – A mix of craftsman-style homes and small multi-story houses.
    - Golden Hill – Historic homes, some with detached garages and hilly terrain.
    - Normal Heights – Bungalow and cottage-style homes with limited driveway space.
    - Logan Heights – Older homes, many with garage-forward layouts.
    - Linda Vista – Some areas have similar compact homes with driveways in front.
    - Serra Mesa – Small, mid-century homes with driveways and garages in front.
    - Rolando Village – Similar narrow lots with a mix of old and new homes.
    - San Carlos – Some older homes in sloped areas.
    - Kensington - Known for its historic Spanish-style homes, Kensington offers narrow lots with front-facing garages.
    - Talmadge - Adjacent to Kensington, Talmadge features older homes with diverse architectural styles, including cottages and bungalows.
    - University Heights - This neighborhood boasts a mix of older homes on compact lots, often with front-facing garages.
    - Mission Hills - Mission Hills is an upscale neighborhood just north of downtown San Diego.
    - Hillcrest - Hillcrest is a vibrant neighborhood with a mix of older single-family homes and apartments.
    - College Area - Located near San Diego State University, the College Area offers diverse housing options, including older homes on narrow lots with front-facing garages.


- While browsing through street views, I found the house in **Kensington** area:
    - Coordinates: `37.9004022,-122.2888087`
    - Google Maps link: https://www.google.com/maps/place/356+Coventry+Rd,+Kensington,+CA+94707,+USA/@37.898787,-122.2958131,15.18z/data=!4m6!3m5!1s0x80857947bba1b0a5:0xb12a3cc68ed056ac!8m2!3d37.900398!4d-122.2862338!16s%2Fg%2F11c250smlw?entry=ttu&g_ep=EgoyMDI1MDMxOS4yIKXMDSoASAFQAw%3D%3D


```
HTB{356_CoventryRoad}
```
