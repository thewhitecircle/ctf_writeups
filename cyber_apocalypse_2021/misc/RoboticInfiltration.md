---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | Robotic Infiltration Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: misc
challenge: Robotic Infiltration
tags: "misc, twh, rosbag, rviz"
date: 2021-04-24T00:00:00+00:00
last_update: 2021-04-24T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## Robotic Infiltration

> Solved by : thewhiteh4t

* We got a `capture.bag` file in this one
* The challenge mentions ROS which leads to a utility called `rosbag`
* rosbag can be used to play this bag file
* Installation and tutorials are given in ros wiki : http://wiki.ros.org/Documentation
* The challenge mentions `rebuild plan for facility`
* after some poking around we saw rosbag comes with another utility known as `rviz`
* rviz can be used for 3d visualization of bag files

**Step 1 : Start roscore**

![](https://i.imgur.com/ZsoFhr6.png)

**Step 2 : Play bag file**

```bash
$ rosbag play capture.bag
```

**Step 3 : Launch rviz**

```bash
$ rosrun rviz rviz
```

* Now all we had to do was tweak things in rviz a little to improve visibility and eventually we spotted the flag

![](https://i.imgur.com/CIeNKFz.png)

* After some play and pause action we got the full flag

```
CHTB{r0s_1s_r0b0tic_p0w3r}
```

![](robotic.gif)