---
layout: load_md
title: The White Circle | Hsctf 2021 | seeded randomizer Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hsctf 2021
parent: hsctf_2021
category: misc
challenge: seeded randomizer
tags: "misc, twh, java, rng"
date: 2021-06-20T00:00:00+00:00
last_modified_at: 2021-06-20T00:00:00+00:00
---

<h1 class="heading card-title white-text">Hsctf 2021</h1>

## seeded randomizer
> Solved By : thewhiteh4t

* we are given a java file with two functions

```java
import java.util.Random;

public class SeededRandomizer {

	public static void display(char[] arr) {
		for (char x: arr)
			System.out.print(x);
		System.out.println();
	}

	public static void sample() {
		Random rand = new Random(79808677);
		char[] test = new char[12];
		int[] b = {9, 3, 4, -1, 62, 26, -37, 75, 83, 11, 30, 3};
		for (int i = 0; i < test.length; i++) {
			int n = rand.nextInt(128) + b[i];
			test[i] = (char)n;
		}
		display(test);
	}

	public static void main(String[] args) {
		// sample();
		// Instantiate another seeded randomizer below (seed is integer between 0 and 1000, exclusive):
		char[] flag = new char[33];
		int[] c = {13, 35, 15, -18, 88, 68, -72, -51, 73, -10, 63, 
				1, 35, -47, 6, -18, 10, 20, -31, 100, -48, 33, -12, 
				13, -24, 11, 20, -16, -10, -76, -63, -18, 118};
		for (int i = 0; i < flag.length; i++) {
			int n = (int)(Math.random() * 128) + c[i];
			flag[i] = (char)n;
		}
		display(flag);
	
	}

}
```

* sample prints "Hello World"
* random is initialized with a constant seed `79808677`

```java
Random rand = new Random(79808677);
```

* this means that the value produced by random will now be constant every time we execute the script
* our flag is in main and this time `Math.random` is used instead of previous approach
* but in the comments we can see that they have mentioned the range of seed which lies between 0 and 1000
* I modified main to bruteforce random with seeds from 0 to 1000

```java
public static void main(String[] args) {
	//sample();
	// Instantiate another seeded randomizer below (seed is integer between 0 and 1000, exclusive):
	char[] flag = new char[33];
	int[] c = {13, 35, 15, -18, 88, 68, -72, -51, 73, -10, 63, 
			1, 35, -47, 6, -18, 10, 20, -31, 100, -48, 33, -12, 
			13, -24, 11, 20, -16, -10, -76, -63, -18, 118};

	for (int x = 0; x < 1001; x++) {
		Random rand = new Random(x);
		for (int i = 0; i < flag.length; i++) {
			int n = rand.nextInt(128) + c[i];
			flag[i] = (char)n;
		}
		display(flag);
	}	
}
```

Output :

![](https://i.imgur.com/ojD1yuq.png)