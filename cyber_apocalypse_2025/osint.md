# OSINT

## The Mechanical Bird's Nest
> Solved by thewhiteh4t


![](https://i.imgur.com/s3EtX94.png)

- For those who are into UAPs and aliens they know what this place is : Area51
- Otherwise a reverse image search is enough
    
https://maps.app.goo.gl/fKMxT9BCjm3qTuNB7

    HTB{37.247_-115.812}


----------


## The Shadowed Sigil
> Solved by thewhiteh4t


- An IP address is given, which is an IoC of APT28 : `139.5.177.205`

https://ioc.one/auth/attribute/edaac7f0-e137-5bdf-a2d3-5377b688275e


    HTB{APT28}


----------


## The Ancient Citadel
> Solved by thewhiteh4t


![](https://i.imgur.com/6HD6pEc.jpeg)

- Reverse image search reveals the name of the place : `castillo brunet`
- Located at : `Iberia 104, 2571409 Viña del Mar, Valparaíso, Chile`

```
    HTB{Iberia_104_2571409_Viña_del_Mar_Valparaíso}
```

----------


## The Poisoned Scroll
> Solved by StarryLord


    CHALLENGE NAME
    The Poisoned Scroll
    In her crystal-lit sanctum, Nyla examines reports of a series of magical attacks against the ruling council of Germinia, Eldoria's eastern ally. The attacks all bear the signature of the Shadow Ravens, a notorious cabal of dark mages known for their espionage across the realms. Her fingers trace connections between affected scrolls and contaminated artifacts, seeking the specific enchantment weapon deployed against the Germinian leaders. The runes along her sleeves pulse rhythmically as she sifts through intercepted messages and magical residue analyses from the attack sites. Her network of information crystals glows brighter as patterns emerge in the magical attacks—each victim touched by the same corrupting spell, though disguised under different manifestations. Finally, the name of the specific dark enchantment materializes in glowing script above her central crystal. Another dangerous threat identified by Eldoria's master information seeker, who knows that even the most sophisticated magical weapons leave distinctive traces for those who know how to read the patterns of corruption.
    Poisoned Scroll: HTB{MalwareName}
    Example: HTB{DarkPhantom} No special characters

After decoding the Eldorian:
Basically we had to find the name of the malware that targeted German politicians:


https://cloud.google.com/blog/topics/threat-intelligence/apt29-wineloader-german-political-parties

    HTB{WineLoader}


----------


## Echoes In Stone
> Solved by Xan0er


![](https://i.imgur.com/UDj7dr7.jpeg)



- After a simple google reverse image search, we get the name of the place where this cross is located: `Muiredach's High Cross`
- Coordinates: `53.7776973,-6.4178868`

```
    HTB{Muriedach_High_Cross}
```

----------


## The Stone That Whispers
> Solved by Xan0er


![](https://i.imgur.com/E94HxWD.png)

- Just like the `Echoes In Stone` challenge, we reversed image search the given image in the challenge and we will get the name of place where this stone is located: `Hill Of Tara`
- After reading through wikipedia article, we get the name of the stone: `Lia Fail`
- Coordinates: `53.5817331,-6.6101392`

```
    HTB{Lia_Fail}
```

----------


## Hillside Haven
> Solved by Xan0er


![](https://i.imgur.com/RM2BxJW.jpeg)

- We need to locate the house shown in the image above.
- Google reverse image gives us multiple hints that this house is located in the california region.
- Also, house number is visible in image: `356`
- I searched through google reverse image results for hours but still couldn’t find this house so I took the help of ChatGPT


- I asked ChatGPT to give me list of cities in california:
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


- I created a prompt to look though cities in **California**
    
    I am going to give you name of a city in california and I want you to list every possible area which might have house structure like shown in the picture. The city name is: <California_City>


- Next, I started looking though each area street views to find neighbourhood where houses structure might match the one in given image


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


- I couldn’t find any clues in **Los Angeles** so the next stop is **San Francisco**
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
    - Google maps link: https://www.google.com/maps/place/356+Coventry+Rd,+Kensington,+CA+94707,+USA/@37.898787,-122.2958131,15.18z/data=!4m6!3m5!1s0x80857947bba1b0a5:0xb12a3cc68ed056ac!8m2!3d37.900398!4d-122.2862338!16s%2Fg%2F11c250smlw?entry=ttu&g_ep=EgoyMDI1MDMxOS4yIKXMDSoASAFQAw%3D%3D


```
HTB{356_CoventryRoad}
```
