---
layout: load_md
title: The White Circle | Wpictf 2021 | wpi admin 3 Writeup
desc: Check out our writeup for wpi admin 3 for Wpictf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: web
challenge: wpi admin 3
tags: "web, twh"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---



> Solved by : thewhiteh4t

* We now have access to admin panel and we have to change grades of our friend
* challenge states that we need to add grades for 4 semesters
* grades are uploaded using a json file
* on inspecting the page source we can see a link for an example json file

![](https://i.imgur.com/PUyXWbj.png)

* by adding two more semesters and changing `production` to true we can upload grades successfully, here is the json I used :

```json
    {
        "configName": "Example",
        "timezone": "EST",
        "production": true,
        "studentData": [
          {
            "name": "Alexo O",
            "email": "alexo@uupeye.edu",
            "id": 123456,
            "major": "Computer Science",
            "class": 2023,
            "workerStatus": false,
            "grades": [
              {
                "year": 2019,
                "semester": "fall",
                "semesterGPA": 4.00,
                "courses": [
                  {
                    "name": "Calculus 1",
                    "code": "MA1021",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Introduction to Program Design",
                    "code": "CS1101",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Elements of Writing",
                    "code": "WR1010",
                    "points": 3,
                    "grade": "A"
                  }, {
                    "name": "Calculus 2",
                    "code": "MA1022",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Accelerated Object-Oriented Design Concepts",
                    "code": "CS2103",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Writing about Science and Technology",
                    "code": "WR1011",
                    "points": 3,
                    "grade": "A"
                  }
                ]
              },
              {
                "year": 2019,
                "semester": "spring",
                "semesterGPA": 4.00,
                "courses": [
                  {
                    "name": "Calculus 3",
                    "code": "MA1023",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Calculus 4",
                    "code": "MA1024",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Systems Programming Concepts",
                    "code": "CS2303",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Algorithms",
                    "code": "CS2223",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "General Physics-mechanics",
                    "code": "PH1110",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "General Physics-electricity and Magnetism",
                    "code": "PH1120",
                    "points": 3,
                    "grade": "A"
                  }
                ]
              },
              {
                "year": 2020,
                "semester": "spring",
                "semesterGPA": 4.00,
                "courses": [
                  {
                    "name": "Calculus 3",
                    "code": "MA1023",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Calculus 4",
                    "code": "MA1024",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Systems Programming Concepts",
                    "code": "CS2303",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Algorithms",
                    "code": "CS2223",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "General Physics-mechanics",
                    "code": "PH1110",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "General Physics-electricity and Magnetism",
                    "code": "PH1120",
                    "points": 3,
                    "grade": "A"
                  }
                ]
              },
              {
                "year": 2020,
                "semester": "summer",
                "semesterGPA": 4.00,
                "courses": [
                  {
                    "name": "Calculus 3",
                    "code": "MA1023",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Calculus 4",
                    "code": "MA1024",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Systems Programming Concepts",
                    "code": "CS2303",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "Algorithms",
                    "code": "CS2223",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "General Physics-mechanics",
                    "code": "PH1110",
                    "points": 3,
                    "grade": "A"
                  },
                  {
                    "name": "General Physics-electricity and Magnetism",
                    "code": "PH1120",
                    "points": 3,
                    "grade": "A"
                  }
                ]
              }
            ]
          }
        ]
      }
```

* after uploading it we get the flag!

![](https://i.imgur.com/OjwLaH4.png)