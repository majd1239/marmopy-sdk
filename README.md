# Marmopy-sdk

# Description
Marmopy-sdk helps python developers to consume Marmo Relayer.

# Simple Summary
Allowing users to sign messages to show intent of execution, but allowing a third party relayer to execute them is an emerging pattern being used in many projects. 
This pattern simplifies the integration with any Ethereum based platform. 
Marmo relayer: (https://github.com/ripio/marmo-relayer). 

# Abstract
### User pain points:
> - Users don't want to think about ether
> - Users want to be able to pay for transactions using whatever they have 
> - Users don’t want to download apps/extensions (at least on the desktop) to connect to their apps

# Ecosystem Graph
![](./images/01.png)

###### General WIKI ecosystem.
- Work in progress.
###### API layer
- Marmo relayer doc: (https://github.com/ripio/marmo-relayer/blob/master/README.md)
###### CORE layer
- Marmo contracts doc: (https://github.com/ripio/marmo-contracts/blob/master/README.md)

# Features
- Complete implementation of Intent functionality for Marmo relay.
- Ethereum wallet support.
- Comprehensive integration tests demonstrating a number of the above scenarios.

##### Runtime dependencies:

- web3py (https://pypi.org/project/web3/).

# Getting started

##### Prerequisites
* Python2 or Python3

Pip install from git url:

```shell
pip install git+https://github.com/ripio/marmopy-sdk
```

### Intent Flowchart
![](./images/02.png)

###### Dependencies
> - T0  -> -
> - T1  -> - 
> - T2  -> T1
> - T3  -> T1
> - T4  -> T1
> - T5  -> T1, T4
> - T6  -> T2
> - T7  -> T2
> - T8  -> T2
> - T9  -> T3
> - T10 -> T4
> - T11 -> T5
> - T12 -> T6
> - T13 -> T7
> - T14 -> T8
> - T15 -> T9
> - T16 -> T10
> - T17 -> T10
> - T18 -> T11
> - T19 -> T13
> - T20 -> T14
> - T21 -> T15
> - T22 -> T18
> - T23 -> T19
> - T24 -> T21
> - T25 -> T22
> - T26 -> T12, T23, T20, T15
> - T27 -> T24
> - T28 -> T25
> - T29 -> T27

### Configure SDK
```python
from marmopy import DefaultConf

DefaultConf.ROPSTEN.as_default()
```

### Create a Marmo wallet
```python
from marmopy import Wallet

wallet = Wallet("Your private key here")
print(wallet.address) # This is your Marmo wallet address!

```

### Build a intent
```python
from marmopy import Intent, IntentAction, DefaultConf

class Contract:
    def __init__(self,contractAddress):
        self.contractAddress = contractAddress
        
    @IntentAction
    def transfer(self, to='address', value='uint256') : return 'bool'
    

erc20 = Contract("0x2f45b6fb2f28a73f110400386da31044b2e953d4")  #example of ERC20 contract with IntentActions injections. 
intentAction = erc20.transfer("0x7F5EB5bB5cF88cfcEe9613368636f458800e62CB", 0)

intent = Intent(intent_action = intentAction)
```

### Build a intent generic	
```python	
from marmopy import Intent, IntentGeneric	
from examples.generic_contract import Contract	

// can load the full abi, in var abi, of any contract for the demonstration mode, 	
// only the necessary part for the example is loaded	
	
abi = """	
[	
	{	
		"constant":false,	
		"inputs":[	
			{	
				"name":"_to",	
				"type":"address"	
			},	
			{	
				"name":"_value",	
				"type":"uint256"	
			}	
		],	
		"name":"transfer",	
		"outputs":[	
			{	
				"name":"success",	
				"type":"bool"	
			}	
		],	
		"payable":false,	
		"type":"function"	
	}	
]	
"""	
    	
token_contract_address = "0x2f45b6fb2f28a73f110400386da31044b2e953d4" #RCN TOKEN	
to = "0xA6693e041aAfE9b9D722338Ca9f8A6e7746d7148"	
data = Contract(abi).transfer({"_to":to, "_value":0})	

intent = IntentGeneric(data, token_contract_address, 0)
```

### Sign and relay intent
```python
signedIntent = wallet.sign(intent)
signedIntent.relay("<Relayer URL>")
```

# Structure of builder

| Name                  | Type/Format   | Mandatory | Default       | Description                                              |
| --------              | --------      | --------  | --------      | --------                                                 |
| id                    | hexstring     | yes       | Autogenerated | A unique identifier for the intent.                      |
| dependencies          | hexstring[]   | no        | Empty         | Define a correlation id for intent.                      |
| signer                | hexstring     | yes       | -             | The address of the signer that sign the intent.          |
| wallet                | hexstring     | no        | Autogenerated | Contract address or Marmo instance.                      |
| salt                  | hexstring     | no        | 0x0           | Use to send the same intent many times if needed.        |
| minGasLimit           | Int           | no        | 0             | Minimum gas price.                                       |
| maxGasPrice           | Int           | no        | 99999999      | Maximum gas price.                                       |
| intentAction          | IntentAction  | yes       | 0x0           | IntentAction Example -> marmopy-sdk.examples.ERC20.     |
| expiration            | Int           | no        | 15            | Contract Expiration Date in Days.                                 |


# Run Tests

First install nose -> pip install nose
Then run:

```shell
 nosetests tests/*
 ```
