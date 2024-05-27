# blockchain

## Navigating the unknown
> Solved by : thewhiteh4t


- Unknown.sol contains our contract with a simple condition i.e. if value `version` is 10 updated is set to `True`
- When we connect with netcat and print flag, value of updated is checked, if value is True the flag is presented
- First we need to get the current value of target address :


![](https://i.imgur.com/zHXSq8o.png)

- Target address is required to interact with the contract
- I used `web3py` to interact with the contract using the following script : 

```python
from web3 import Web3
from solcx import compile_source

w3 = Web3(Web3.HTTPProvider('http://167.172.50.208:32499'))

print(f'Connected : {w3.is_connected()}')

target = '0xBB5Cd458D2691070a1c9460C2d5882253e36a5c0'

with open('Unknown.sol', 'r') as solfile:
        sol_code = solfile.read()

compiled_sol = compile_source(sol_code, output_values=['abi', 'bin'])
contract_id, contract_interface = compiled_sol.popitem()
abi = contract_interface['abi']

print('Updating Value to 10')
contract_instance = w3.eth.contract(address=target, abi=abi)
contract_instance.functions.updateSensors(10).transact()
```


- Script first checks connection with the rpc endpoint
- After that it compiles the contract source code, this is required to get value of `abi` i.e. `Application Binary Interface`
- Its a sort of definition of the functions and data types present in the contract
- abi of unknown.sol :

```json
    [
            {
                    'inputs': [
                            {
                                    'internalType': 'uint256',
                                    'name': 'version',
                                    'type': 'uint256'
                            }
                    ],
                    'name': 'updateSensors', 
                    'outputs': [], 
                    'stateMutability': 'nonpayable', 
                    'type': 'function'
            }, 
            {
                    'inputs': [],
                    'name': 'updated', 
                    'outputs': 
                            [
                                    {
                                            'internalType': 'bool', 
                                            'name': '', 
                                            'type': 'bool'
                                    }
                            ],
                    'stateMutability': 'view',
                    'type': 'function'
            }
    ]
```

- So in this we can see our target function name is `updateSensors`
- Finally script calls transact function on updateSensors with argument 10 which is the value of version required to set updated to True
- After running the script and going back to netcat we can print the flag


![](https://i.imgur.com/hvXqjqQ.png)

### nigamelastic's method

A lot of stuff to install
web3py
https://remix.ethereum.org/#lang=en&optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.18+commit.87f61d96.js

Target details:


    Private key     :  0x9ccac2d9cb08e106e616725eb93ae220b74e45f84fcba7144385cbbf50be4287
    Address         :  0x2F89F82055E803b470Da977C8260B4B25d0Fc43B
    Target contract :  0x91FCa15f2602469894850ACEd90A6Fa523338e5d
    Setup contract  :  0x00Ac07B771a835CA9E31A38B2FFFB2F1695dF6Fe

connected to rpc url:


     kali  python3
    Python 3.11.2 (main, Feb 12 2023, 00:48:52) [GCC 12.2.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> from web3 import Web3
    >>> rpc_url="http://144.126.196.198:31920"
    >>> web3 = Web3(Web3.HTTPProvider(rpc_url))
    >>> web3.isConnected()

Checking stuff in ur private key:


    private_key = '0x9ccac2d9cb08e106e616725eb93ae220b74e45f84fcba7'
    account: LocalAccount = Account.from_key(private_key)
    print(f"Your hot wallet address is {account.address}")
    public_address='0x2F89F82055E803b470Da977C8260B4B25d0Fc43B'
    >>> balance = web3.eth.get_balance(public_address)
    >>> print(balance)
    5000000000000000000000
    web3.default_account = account.address
    target_contract='0x91FCa15f2602469894850ACEd90A6Fa523338e5d'
    from web3.gas_strategies.rpc import rpc_gas_price_strategy
    web3.eth.set_gas_price_strategy(rpc_gas_price_strategy)

running transaction keeps giving me error


    
    Traceback (most recent call last):
      File "/home/kali/Desktop/repos/Writeups/cyberapocalypse/cyberapolcalypse2023/blockchain/blockchain_navigating_the_unknown/solution2.py", line 6, in <module>
        print(w3.isConnected())
              ^^^^^^^^^^^^^^
    AttributeError: 'Web3' object has no attribute 'isConnected'. Did you mean: 'is_connected'?
    

yeah all of that is BS, screw python learn nodejs like this:


    const Web3 = require('web3');
    const abi = [
        {
            "inputs": [
                {
                    "internalType": "uint256",
                    "name": "version",
                    "type": "uint256"
                }
            ],
            "name": "updateSensors",
            "outputs": [],
            "stateMutability": "nonpayable",
            "type": "function"
        },
        {
            "inputs": [],
            "name": "updated",
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
    const contractAddress = '0x7b92F8C66e04b3cc857218EeFaA433eab35F10d0';
    const rpcUrl = 'http://188.166.152.84:30963';
    const web3 = new Web3(new Web3.providers.HttpProvider(rpcUrl));
    const contract = new web3.eth.Contract(abi, contractAddress);
    const version = 10;
    contract.methods.updateSensors(version).send({ from: '0x5Bb68dc361f80fabB55916189af56d850d98FFDE' }, (error, txHash) => {
        if (error) {
            console.log('Error:', error);
        } else {
            console.log('Transaction hash:', txHash);
        }
    });
    

to get the flag

----------


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


