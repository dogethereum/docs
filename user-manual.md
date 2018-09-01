# Dogecoin <-> Ethereum bridge - User guide

**WARNING: THIS IS AN ALPHA RELEASE. SOME FEATURES STILL HAVE TO BE IMPLEMENTED AND THERE MIGHT BE BUGS. JUST USE IT WITH COINS YOU ARE OPEN TO LOSE.**

To get a general overview of the system, go to: https://github.com/dogethereum/docs

A "user" of the bridge can do the following operations:
* Lock (Doge -> Eth).
* Transfer doge tokens (Eth -> Eth).
* Unlock (Eth -> Doge).

The "alpha" bridge uses Dogecoin mainnet and Ethereum Rinkeby testnet.

The doge token is deployed at address [0xd41d4b54013a622eaec3d6f35072b6a5e827c0ef](https://rinkeby.etherscan.io/token/0xd41d4b54013a622eaec3d6f35072b6a5e827c0ef)

## Prerequisites

In order to use the bridge you will need:

* An Ethereum node running on rinkeby testnet with its blockchain up to date and rpc interface enabled. 
<pre>
geth --rinkeby --syncmode=fast --cache=2048 --rpc --rpcapi="db,eth,net,web3,personal" console
</pre>
* Some Rinkeby Eth to pay the network transaction fees. You can get some on the [faucet](https://faucet.rinkeby.io).
* A Dogecoin core node running on mainnet with its blockchain up to date and rpc interface enabled (Other doge wallets won't work).
<pre>
<i>dogecoindOrDogecoinQtCommand</i> -server -rpcuser=<i>yourDogeRpcUser</i> -rpcpassword=<i>yourDogeRpcPassword</i> 
</pre>
* Some mainnet Doges on your Dogecoin-Qt wallet. You can get some on [this faucet](https://freedoge.co.in) or [this other faucet](https://coinpot.co).
* Install [Dogethereum tools](https://github.com/dogethereum/dogethereum-tools) : See [prerequisites](https://github.com/dogethereum/dogethereum-tools#requirements) and [installation instructions](https://github.com/dogethereum/dogethereum-tools#installation).

## Converting Dogecoin doges to Ethereum doge tokens.

Before you start go to the dogethereum tools directory: 
```
cd dogethereum-tools
```

**Send 8 doges to ethereum**

<pre>
node user/lock.js --dogeuser <i>yourDogeRpcUser</i> --dogepassword <i>yourDogeRpcPassword</i> --value 800000000
</pre>

The minumum value to lock is 3 doges.

This will:
* Select an operator that is available to receive 8 doges.
* Create, sign and broadcast a doge tx sending 8 doges to the operator.
* Print your private key. You need to use it later on to get the doge tokens. 

Expected output:
<pre>
Lock 8 doges.
Connected to dogecoin node!
Initiating lock... 
Locking 8 doges to address <i>OperatorDogeAddress</i> using operator <i>0xoperatorPublicKeyHash</i>
Sent doge tx <i>...</i>
User private key : <i>0xYourPrivateKey</i>
User eth address : <i>0xYourEthAddress</i>
Total locked 8 doges.
Lock Done.
</pre>

**Import your private key**

The doge tokens will be assigned to the ethereum address controlled by the private key that signed the 1st input of the dogecoin transaction.
That is the private key that you obtained on the previous step.
In order to use your doge tokens, you need import your private key to ethereum:
<pre>
node user/import-doge-key-to-eth.js --privateKey <i>0xYourPrivateKey</i>
</pre>

**Check you have received the tokens**

To check you have received the tokens (after ~20 minutes):
<pre>
node user/print-balances.js --address <i>0xYourEthAddress</i>
</pre>

There are fees you have to pay for doing a lock.
If you sent 8 doges, you will receive 6 doge tokens because the bridge fee is 2% of the locked amount, minimum 2 doges.
On top of that, you will be charged ~1 doge to pay the doge tx fee.

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
The bridge fee is 1% of the unlocked amount, minimum 1 doge.
On top of that ~1.5 doge will be deducted to pay the doge tx fee.
If you sent 5 doge tokens you will receive ~2.5 doges.

After ~2 minutes you will receive a doge tx to <i>yourDogeAddress</i>.

You can check you received the doges using your Dogecoin wallet or https://dogechain.info

Congratulations! You converted doge tokens to doges!

## Dogethereum tools reference documentation
For a full command reference:
* See [User tools reference](https://github.com/dogethereum/dogethereum-tools#user-tools).
* On the command line: <pre>node user/<i>whatever-command-you-want-to-use.js</i> --help</pre>
