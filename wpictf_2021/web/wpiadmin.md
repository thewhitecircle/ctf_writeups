---
layout: load_md
title: The White Circle | Wpictf 2021 | wpi admin Writeup
desc: Check out our writeup for wpi admin for Wpictf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: web
challenge: wpi admin
tags: "web, twh"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---



> Solved by : thewhiteh4t

* email of our friend : alexo@uupeye.edu
* “students” here have some “bad” passwords
* emails of students are visible on : https://wpiadmin.wpictf.xyz/topStudents
* so we get a list of emails which we can bruteforce on student login!

```
    colino@uupeye.edu
    calliep@uupeye.edu
    annar@uupeye.edu
    gaylenek@uupeye.edu
    dennisb@uupeye.edu
    sherrim@uupeye.edu
    adams@uupeye.edu
```

* after bruteforcing we get all these credentials

```
    colino@uupeye.edu:123456
    calliep@uupeye.edu:password
    annar@uupeye.edu:iloveyou
    gaylenek@uupeye.edu:qwerty
    dennisb@uupeye.edu:123123
    sherrim@uupeye.edu:12345678
    adams@uupeye.edu:soccer
```

* after checking each profile we get flag on `dennisb@uupeye.edu`

![](https://imgur.com/a/pt0esJv)

