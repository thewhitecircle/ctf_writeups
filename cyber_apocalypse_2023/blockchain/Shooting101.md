---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Shooting 101 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: blockchain
challenge: Shooting 101
tags: "blockchain, twh, nigamelastic, solidity, web3, python"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>


## Shooting 101
> Solved by : thewhiteh4t


- In this challenge we are introduced with `fallback` , `receive`  and `modifiers`
- in ShootingArea.sol three modifiers are present like :

```
    modifier firstTarget() {
            require(!firstShot && !secondShot && !thirdShot);
            _;
        }
```

- Require as the name suggests waits for the conditions to become satisfactory
- when the conditions are satisfied it uses a wildcard merge i.e. `_;`
- firstTarget modifier is mentioned in the following section of the code :

```
    fallback() external payable firstTarget {
            firstShot = true;
        }
```

- This means that fallback() will only execute when conditions in firstTarget are satisfied
- when fallback will run `firstShot` will become `True`
- similarly there are two more modifiers with different conditions
- in my understanding (don’t trust this) fallback is called if a non existent function is called or if random data is sent to the contract in a transaction
- after calling fallback receive is usable and finally third function is usable
- while trying I was trying to use `transact()`  on them but they were throwing errors
- they work in the specific order in this challenge (maybe?)
- Here is the script :


```python
from web3 import Web3
from web3 import exceptions
from solcx import compile_source
import time

w3 = Web3(Web3.HTTPProvider('http://104.248.169.177:30380'))

print(f'Connected : {w3.is_connected()}\n')

target = '0x09c3Dfe533774564a7761b5fEC15Ff5b0264Ec64'
addr = '0x73Fca728DbD9592D28DF01f8a63427912498a50E'

with open('ShootingArea.sol', 'r') as solfile:
    sol_code = solfile.read()

compiled_sol = compile_source(sol_code, output_values=['abi', 'bin'])
contract_id, contract_interface = compiled_sol.popitem()
abi = contract_interface['abi']

contract_instance = w3.eth.contract(address=target, abi=abi)

f_shot = contract_instance.functions.firstShot().call()
s_shot = contract_instance.functions.secondShot().call()
t_shot = contract_instance.functions.thirdShot().call()

print(f'First Shot : {f_shot}')
print(f'Second Shot : {s_shot}')
print(f'Third Shot : {t_shot}\n')


print('Hitting First Target...')
w3.eth.send_transaction({
    'to': target,
    'from': addr,
    'value': 0,
    'data': '0x1'
})
upd_f_shot = contract_instance.functions.firstShot().call()
print(f'First Shot : {upd_f_shot}')

print('Hitting Second Target...')
contract_instance.receive.transact()
upd_s_shot = contract_instance.functions.secondShot().call()
print(f'Second Shot : {upd_s_shot}')

print('Hitting Third Target...')
contract_instance.functions.third().transact()
upd_t_shot = contract_instance.functions.thirdShot().call()
print(f'Third Shot : {upd_t_shot}')
```


![](https://i.imgur.com/LZqv0jz.png)

- To solve we need to hit all three targets i.e. turn all to True :


![](https://i.imgur.com/JBZvlfz.png)


### nigamelastic's method

    const Web3 = require('web3');
    const web3 = new Web3('http://165.232.108.240:31803');
    
    // import the ABI of the ShootingArea and Setup contracts
    const shootingAreaABI = [{"inputs":[],"stateMutability":"payable","type":"constructor"},{"inputs":[],"name":"firstShot","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"secondShot","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"thirdShot","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"fallback","outputs":[],"stateMutability":"payable","type":"function"},{"inputs":[{"internalType":"bool","name":"","type":"bool"}],"name":"firstTarget","outputs":[],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"bool","name":"","type":"bool"}],"name":"secondTarget","outputs":[],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"bool","name":"","type":"bool"}],"name":"thirdTarget","outputs":[],"stateMutability":"view","type":"function"},{"inputs":[],"name":"third","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"receive","outputs":[],"stateMutability":"payable","type":"function"}];
    const setupABI = [{"inputs":[],"stateMutability":"nonpayable","type":"constructor"},{"inputs":[],"name":"isSolved","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"view","type":"function"}];
    
    // create contract instances
    const shootingAreaAddress = '0xAbfA937f5DAA74d1a85b4e57Ad129be70b4C31c0';
    const shootingAreaContract = new web3.eth.Contract(shootingAreaABI, shootingAreaAddress);
    
    const setupAddress = '0xc1023661c8d6A9d19c6fC9a76f7124b843E41eEF';
    const setupContract = new web3.eth.Contract(setupABI, setupAddress);
    
    async function solvePuzzle() {
      // send a transaction to the fallback function of the ShootingArea contract
      const tx = await web3.eth.sendTransaction({
        to: shootingAreaAddress,
        value: web3.utils.toWei('1', 'ether')
      });
      console.log(`Sent transaction to ShootingArea fallback function. Transaction hash: ${tx.transactionHash}`);
    
      // send a transaction to the receive function of the ShootingArea contract
      const tx2 = await web3.eth.sendTransaction({
        to: shootingAreaAddress,
        value: web3.utils.toWei('1', 'ether')
      });
      console.log(`Sent transaction to ShootingArea receive function. Transaction hash: ${tx2.transactionHash}`);
    
      // call the third function of the ShootingArea contract
      const tx3 = await shootingAreaContract.methods.third().send();
      console.log(`Called ShootingArea third function. Transaction hash: ${tx3.transactionHash}`);
    
      // check if the puzzle is solved
      const isSolved = await setupContract.methods.isSolved().call();
      console.log(`Puzzle solved? ${isSolved}`);
    }
    
    solvePuzzle();
    

yeah that didnt work but we see fallback function

as per 

https://www.geeksforgeeks.org/solidity-fall-back-function/


    Only one unnamed function can be assigned to a contract and it is executed whenever the contract receives plain Ether without any data

I am using this to check the boolean value of all the three flags:


    
    const Web3 = require('web3');
    const web3 = new Web3(new Web3.providers.HttpProvider('http://165.232.100.46:31589'));
    const YOUR_ADDRESS = '0x925A577a42A87bb4B364e9fc8D987779f5B05442';
    const fromAddress = YOUR_ADDRESS
    const setupAbi = [
      {
        "inputs": [],
        "stateMutability": "nonpayable",
        "type": "constructor"
      },
      {
        "inputs": [],
        "name": "TARGET",
        "outputs": [
          {
            "internalType": "contract ShootingArea",
            "name": "",
            "type": "address"
          }
        ],
        "stateMutability": "view",
        "type": "function"
      },
      {
        "inputs": [],
        "name": "isSolved",
        "outputs": [
          {
            "internalType": "bool",
            "name": "",
            "type": "bool"
          }
        ],
        "stateMutability": "view",
        "type": "function"
      }
    ];
    const shootingAreaAbi = [
      {
        "inputs": [],
        "name": "firstShot",
        "outputs": [
          {
            "internalType": "bool",
            "name": "",
            "type": "bool"
          }
        ],
        "stateMutability": "view",
        "type": "function"
      },
      {
        "inputs": [],
        "name": "secondShot",
        "outputs": [
          {
            "internalType": "bool",
            "name": "",
            "type": "bool"
          }
        ],
        "stateMutability": "view",
        "type": "function"
      },
      {
        "inputs": [],
        "name": "thirdShot",
        "outputs": [
          {
            "internalType": "bool",
            "name": "",
            "type": "bool"
          }
        ],
        "stateMutability": "view",
        "type": "function"
      }
    ];
    // Import web3 library and contract ABI
    const abi = shootingAreaAbi;
    
    // Create contract instance
    const contractAddress = '0x010953B8CBe4dF657D80EDd832825A1d1Fc0972D';
    const shootingArea = new web3.eth.Contract(abi, contractAddress);
    // Call getter functions for firstShot, secondShot, and thirdShot
    shootingArea.methods.firstShot().call((err, result) => {
      if (err) {
        console.error(err);
        return;
      }
      console.log(`First shot: ${result}`);
    });
    shootingArea.methods.secondShot().call((err, result) => {
      if (err) {
        console.error(err);
        return;
      }
      console.log(`Second shot: ${result}`);
    });
    shootingArea.methods.thirdShot().call((err, result) => {
      if (err) {
        console.error(err);
        return;
      }
      console.log(`Third shot: ${result}`);
    });
    
    web3.eth.getBalance(fromAddress, (error, result) => {
      if (error) {
        console.error(error);
      } else {
        console.log('Balance in wei:', result);
        console.log('Balance in ether:', web3.utils.fromWei(result, 'ether'));
      }
    });
    /* txHash='0xcf1cfac084f18e9795a79d8f04be0403b12b5536cd1eb56ab206246fd0442d80'
    web3.eth.getTransactionReceipt(txHash, function(err, receipt) {
      console.log(receipt);
    });
     */

basically:

Use for first we need to trigger fallback() without receive() :
in order to trigger fallback we need to send a transaction with 0 data so use this:


    try {
        const tx1 = await web3.eth.sendTransaction({
            to: contractAddress,
            value: web3.utils.toWei('1', 'ether'),
            from: fromAddress,
            data: '00' //comment this for second shot
        });
        } catch (error) {
        console.log('Error calling functions:', error);
    }

for second shot aka receive run following:


    try {
        const tx1 = await web3.eth.sendTransaction({
            to: contractAddress,
            value: web3.utils.toWei('1', 'ether'),
            from: fromAddress,
          
        });
        } catch (error) {
        console.log('Error calling functions:', error);
    

For the third run this:


    const contract = new web3.eth.Contract(contractAbi, contractAddress);
    
    
    contract.methods.third().send({ from: '0x34Cf03f8487C773c36bfd3A632178c501C62e7d7' }, (error, txHash) => {
        if (error) {
            console.log('Error:', error);
        } else {
            console.log('Transaction hash:', txHash);
        }
    });

All three conditions met we get the flag


