---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Navigating the unknown Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: blockchain
challenge: Navigating the unknown
tags: "blockchain, twh, nigamelastic, web3, solidity, python"
date: 2023-03-27T00:00:00+00:00
last_update: 2023-03-27T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>

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

