# Crypto

## Nintendo Base64

> Solved by : thewhiteh4t

Cyberchef recipe :

```json
    [
      { "op": "Find / Replace",
        "args": [{ "option": "Regex", "string": " " }, "", true, false, true, false] },
      { "op": "Find / Replace",
        "args": [{ "option": "Regex", "string": "\\n" }, "", true, false, true, false] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] },
      { "op": "From Base64",
        "args": ["A-Za-z0-9+/=", true] }
    ]
```

---

## PhaseStream 1

> Solved by: Legend

* Each character will be XOR with each character of the key and the length of the key is 5 characters.

```python
    start =  bytearray.fromhex('2e313f2702')  #Randomly tried from starting of given cipher
    text = 'CHTB{'.encode()
    
    key = b''
    output = b''
    
    for i in range(len(start)):
            key += bytes([text[i] ^ start[i]])
    
    print('Key: ' + str(key))
    print('Key len: '+ str(len(key)))
    print('Key type: ' + str(type(key)))
    print('Key hex: ' + key.hex())
    
    for i in range(int(len(start))):
            output += bytes([start[i] ^ key[i % len(key)]])
    
    print('2e313f2702 --> '+ '(' + output.hex() + ')' + ' == ' + str(output) + '(text)')
    
    shifr  = bytearray.fromhex('2e313f2702184c5a0b1e321205550e03261b094d5c171f56011904')
    
    output2 = b''
    
    for i in range(len(shifr)):
            output2 += bytes([shifr[i] ^ key[i % len(key)]])
    
    output_dec = output2.decode(errors='ignore')
    
    print('Flag: '+ output_dec)
    print('Flag hex: '+ output.hex())

Flag: CHTB{u51ng_kn0wn_pl41nt3xt}
```

---

## SoulCrabber 1

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
