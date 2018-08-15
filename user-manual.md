# Dogecoin <-> Ethereum bridge - User guide

**WARNING: THIS IS AN ALPHA RELEASE. SOME FEATURES STILL HAS TO BE IMPLEMENTED AND THERE MIGHT BE BUGS. JUST USE IT WITH COINS YOU ARE OPEN TO LOOSE.**

To get a general overview of the system, go to: https://github.com/dogethereum/docs

A "user" of the bridge can do the following operations:
* Lock (Doge -> Eth).
* Transfer doge tokens (Eth -> Eth).
* Unlock (Eth -> Doge).

The "alpha" bridge uses Dogecoin mainnet and Ethereum Rinkeby testnet.

The doge token is deployed at 
https://rinkeby.etherscan.io/token/0x.....

## Prerequisites

In order to use the bridge you will need:

* An Ethereum node running on rinkeby testnet with its blockchain up to date and rpc interface enabled. 
```
geth --rinkeby --syncmode=fast --cache=2048 --rpc --rpcapi="db,eth,net,web3,personal" console
```
* Some Rinkeby Eth to pay the network transaction fees. You can get some here https://faucet.rinkeby.io
* A Dogecoin node running on mainnet with its blockchain up to date and rpc interface enabled.
```
Dogecoin-Qt -server -rpcuser=_yourDogeRpcUser_ -rpcpassword=_yourDogeRpcPassword_ 
```
* Some mainnet Doges to send to eth. You will be able to get them back (minus fees)
* dogethereum tools
 * Get the prerequisites: https://github.com/dogethereum/dogethereum-tools#requirements
 * Follow the instructions here: https://github.com/dogethereum/dogethereum-tools#installation

## Reference

For a full command reference 
* Visit https://github.com/dogethereum/dogethereum-tools#user-tools 
* On the command line, cd to the the dogethereum-tools directory and type `node user/whatever-comand-you-want-to-use.js --help`


## Converting Dogecoin doges to Ethereum doge tokens.

### Send 10 doges to ethereum

```
node user/lock.js --dogeuser _yourDogeRpcUser_ --dogepassword _yourDogeRpcPassword_ --value 1000000000
```

The minumum value to lock is 3 doges.

This will:
* Select an operator that is available to receive 10 doges.
* Create, sign and broadcast a doge tx sending 10 doges to the operator.
* Print your private key. You need to use it later on to get the doge tokens. 

Expected output:
```
Lock 10 doges.
Connected to dogecoin node!
Initiating lock... 
Locking 10 doges to address _operatorDogeAddress_ using operator _0xoperatorPublicKeyHash_
Sent doge tx _0x..._
User private key : _0xYourPrivateKey_
User eth address : _0xYourEthAddress_
Total locked 10 doges
Lock Done.
```

### Import your private key

The doge tokens will be assigned to the ethereum address controlled by the private key that signed the 1st input of the dogecoin transaction.
That is the private key that you obtained on the previous step.
In order to use your doge tokens, you need import your private key to ethereum:
```
node user/import-doge-key-to-eth.js --privateKey _0xYourPrivateKey_
```
Expected output:
```
Import doge key to eth _0xYourPrivateKey_, password <empty>, unlock true, unlock time <forever>.
Imported key for address _0xYourEthAddress_
Unlocked address for <forever> seconds.
Import and unlock done.
```

### Check you have received the tokens

To check you have received the tokens (after ~20 minutes):
```
node user/print-balances.js --address _0xYourEthAddress_
```
Expected output:
```
Print eth and doge token balances for eth address _0xYourEthAddress_
Eth balance : 0 eth.
Doge token balance : 8 doge tokens.
```
There are fees you have to pay for doing a lock.
If you sent 10 doges you will receive 8 doge tokens.
The fee is 2% of the locked amount, minimum 2 doges.

Congratulations! You converted doges to doge tokens!


## Using the doge tokens

If you received tokens by locking doges, you may want to use them.
The first thing is to make sure you have some eth balance on the address where you have the doge tokens (you probably don't) to pay eth tx fees.
You can send some eth to that address using your preferred ethereum rinkeby wallet.
In case you don't have one, you can use this command (the sender address should have some rinkeby eth) :
```
node user/transfer-eth.js --sender _0x..._ --receiver _0xYourEthAddress_ --value 10000000000000000
```
Expected output:
```
Transfer 0.01 eth from _0x..._ to _0xYourEthAddress_
Sender eth balance : ... eth. Please, make sure that is enough to pay for the tx fee.
Receiver balance : 0 eth.
Sending transaction...
Transaction sent.
Waiting transaction to be included in a block...
Transaction included in a block.
Sender balance : ... eth.
Receiver balance : 0.01 eth.
Transfer eth completed.
```

To transfer 0.01 doge tokens to another user:
```
node user/transfer-tokens.js --sender _0xYourEthAddress_ --receiver _0xDestinationEthAddress_ --value 1000000
```
Expected output:
```
Transfer 0.01 doge tokens from _0xYourEthAddress_ to _0xDestinationEthAddress_
Sender eth balance : ... eth. Please, make sure that is enough to pay for the tx fee.
Sender doge token balance : 8 doge tokens.
Receiver doge token balance : 0 doge tokens.
Initiating transfer... 
Transfer done.
Sender doge token balance : 7.99 doge tokens.
Receiver doge token balance : 0.01 doge tokens.
```

If you received tokens from another user:
You can use them in your preferred rinkeby wallet as any other ERC-20 token 


## Converting Ethereum doge tokens back to Dogecoin doges.

To send 5 doge tokens back to doge:
```
node user/unlock.js --sender _0xYourEthAddress_ --receiver _yourDogeAddress_ --value 500000000
```
Expected output:
```
Unlock 5 doge tokens from _0xYourEthAddress_ to _yourDogeAddress_.
Sender eth balance : ... eth. Please, make sure that is enough to pay for the tx fee.
Sender doge token balance : 8 doge tokens.
Initiating unlock... 
Unlocking 5 doge tokens using operator _0x..._
Total unlocked 5 doge tokens from _0xYourEthAddress_
Unlock done.
Sender doge token balance : 3 doge tokens.
```

The minumum value to unlock is 3 doge tokens.
There are fees you have to pay for doing an unlock.
If you sent 5 doge tokens you will receive 4 doges.
The fee is 1% of the unlocked amount, minimum 1 doge.

After ~2 minutes you will receive a doge tx to `_yourDogeAddress_`.

You can check you received the doges using your Dogecoin wallet or https://dogechain.info
