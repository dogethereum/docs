# Dogecoin <-> Ethereum bridge - User guide

**WARNING: THIS IS AN ALPHA RELEASE. SOME FEATURES STILL HAVE TO BE IMPLEMENTED AND THERE MIGHT BE BUGS. JUST USE IT WITH COINS YOU ARE OPEN TO LOOSE.**

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
<pre>
geth --rinkeby --syncmode=fast --cache=2048 --rpc --rpcapi="db,eth,net,web3,personal" console
</pre>
* Some Rinkeby Eth to pay the network transaction fees. You can get some here https://faucet.rinkeby.io
* A Dogecoin node running on mainnet with its blockchain up to date and rpc interface enabled.
<pre>
Dogecoin-Qt -server -rpcuser=<i>yourDogeRpcUser</i> -rpcpassword=<i>yourDogeRpcPassword</i> 
</pre>
* Some mainnet Doges to send to eth. You will be able to get them back (minus fees)
* Install dogethereum tools
  * Get the prerequisites: https://github.com/dogethereum/dogethereum-tools#requirements
  * Follow the installation instructions: https://github.com/dogethereum/dogethereum-tools#installation

## Reference

For a full command reference 
* Visit https://github.com/dogethereum/dogethereum-tools#user-tools 
* On the command line, cd to the the dogethereum-tools directory and type <pre>node user/<i>whatever-comand-you-want-to-use.js</i> --help</pre>


## Converting Dogecoin doges to Ethereum doge tokens.

### Send 10 doges to ethereum

<pre>
node user/lock.js --dogeuser <i>yourDogeRpcUser</i> --dogepassword <i>yourDogeRpcPassword</i> --value 1000000000
</pre>

The minumum value to lock is 3 doges.

This will:
* Select an operator that is available to receive 10 doges.
* Create, sign and broadcast a doge tx sending 10 doges to the operator.
* Print your private key. You need to use it later on to get the doge tokens. 

Expected output:
<pre>
Lock 10 doges.
Connected to dogecoin node!
Initiating lock... 
Locking 10 doges to address <i>OperatorDogeAddress</i> using operator <i>0xoperatorPublicKeyHash</i>
Sent doge tx <i>0x...</i>
User private key : <i>0xYourPrivateKey</i>
User eth address : <i>0xYourEthAddress</i>
Total locked 10 doges
Lock Done.
</pre>

### Import your private key

The doge tokens will be assigned to the ethereum address controlled by the private key that signed the 1st input of the dogecoin transaction.
That is the private key that you obtained on the previous step.
In order to use your doge tokens, you need import your private key to ethereum:
<pre>
node user/import-doge-key-to-eth.js --privateKey <i>0xYourPrivateKey</i>
</pre>

### Check you have received the tokens

To check you have received the tokens (after ~20 minutes):
<pre>
node user/print-balances.js --address <i>0xYourEthAddress</i>
</pre>

There are fees you have to pay for doing a lock.
If you sent 10 doges you will receive 8 doge tokens.
The fee is 2% of the locked amount, minimum 2 doges.

Congratulations! You converted doges to doge tokens!


## Using the doge tokens

If you received tokens by locking doges, you may want to use them.
The first thing is to make sure you have some eth balance on the address where you have the doge tokens (you probably don't) to pay eth tx fees.
You can send some eth to that address using your preferred ethereum rinkeby wallet.
In case you don't have one, you can use this command (the sender address should have some rinkeby eth) :
<pre>
node user/transfer-eth.js --sender <i>0x...</i> --receiver <i>0xYourEthAddress</i> --value 10000000000000000
</pre>

To transfer 0.01 doge tokens to another user:
<pre>
node user/transfer-tokens.js --sender <i>0xYourEthAddress</i> --receiver <i>0xDestinationEthAddress</i> --value 1000000
</pre>

If you received tokens from another user:
You can use them in your preferred rinkeby wallet as any other ERC-20 token 


## Converting Ethereum doge tokens back to Dogecoin doges.

To send 5 doge tokens back to doge:
<pre>
node user/unlock.js --sender <i>0xYourEthAddress</i> --receiver <i>yourDogeAddress</i> --value 500000000
</pre>

The minumum value to unlock is 3 doge tokens.
There are fees you have to pay for doing an unlock.
If you sent 5 doge tokens you will receive 4 doges.
The fee is 1% of the unlocked amount, minimum 1 doge.

After ~2 minutes you will receive a doge tx to <i>yourDogeAddress</i>.

You can check you received the doges using your Dogecoin wallet or https://dogechain.info

Congratulations! You converted doge tokens to doges!
