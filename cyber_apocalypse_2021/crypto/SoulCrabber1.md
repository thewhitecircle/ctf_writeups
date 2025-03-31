---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | SoulCrabber 1 Writeup
desc: Check out our writeup for SoulCrabber 1 for Cyber Apocalypse 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: crypto
challenge: SoulCrabber 1
tags: "crypto, ava, twh, xor, rust, random"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---



> Solved by : ava and thewhiteh4t

Solution :

* since the seed remains fixed and we know the seed the randomized values in each run will be same so we just have to inverse XOR the cipher text to get the flag
* hex decode cipher text
* convert each char to 8 bit int
* xor against random 8 bit integers
* convert integer to char

XOR Function :

```rust
    fn rand_xor(input : String) -> String {
        
        // get_rng function is called
        
        let mut rng = get_rng();
        return input
            .chars()        // converts string into array of chars
            .into_iter()    // converts vector into iterable
            // map applies a function to each element
            // {:02x} converts integers to 2 char hex format
            // u8 is 8 bit unsigned integer
            // ^ is bitwise XOR
            // XOR against random 8 bit integer
            .map(|c| format!("{:02x}", (c as u8 ^ rng.gen::<u8>())))
            .collect::<Vec<String>>()
            .join("");
    }
```

Get Numbers from hex string :

```rust
    fn rev_xor(input : String) {
        let mut rng = get_rng();
        let inp_arr = hex::decode(input);
        println!("{:?}", inp_arr);
    ...
    [27, 89, 20, 132, 219, 150, 47, 119, 130, 209, 65, 10, 250, 74, 56, 143, 121, 48, 6, 123, 206, 246, 223, 84, 106, 87, 217, 248, 115]
```

use those numbers into array and iterate it over :

```rust
    use rand::{Rng,SeedableRng};
    use rand::rngs::StdRng;
    use std::fs;
    use std::io::Write;
    
    fn get_rng() -> StdRng {
        let seed = 13371337;
        return StdRng::seed_from_u64(seed);
    }
    
    fn rand_xor(input : String) -> String {
        let mut rng = get_rng();
        //print!("{:?}",rng.gen::<u8>());
        let arr = [27, 89, 20, 132, 219, 150, 47, 119, 130, 209, 65, 10, 250, 74, 56, 143, 121, 48, 6, 123, 206, 246, 223, 84, 106, 87, 217, 248, 115];
        let space = "\t";
        for i in &arr
        {
            print!("{}", (i ^ rng.gen::<u8>()));
            print!("{}", space);
        }
        return input
            }
    
    fn main() -> std::io::Result<()> {
        let flag = fs::read_to_string("flag.txt")?;
        let xored = rand_xor(flag);
        //println!("{}", xored);
        let mut file = fs::File::create("out.txt")?;
        file.write(xored.as_bytes())?;
        Ok(())
    }
```
