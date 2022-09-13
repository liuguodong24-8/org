# Welcome, Blockchain developers!

These are tools for blockchain developers. The command line tools will allow you to connect your server to or run your application on the FFF blockchain – or your own private blockchain.

## Clients

For security purposes, three independent implementations were created for FFF. The clients have almost identical functionality, so the one you pick is left to personal choice on platform, language and what your planned use is for the network.

### FFFnode

![Logo for Go](/images/icons/gopher.png)

The **Go** implementation is called **FFFnode**. FFFnode has been audited for security and will be the future basis for the enduser-facing **3f-metamask-extension**, so if you have experience with web development and are interested in building frontends for dapps, you should experiment with FFFnode.

## bsc-genesis-contracts

This repo hold all the genesis contracts on Finance Future Factory chain. More details in [doc-site](https://docs.bnbchain.org/docs/learn/system-contract).

### Prepare

Install dependency:
```shell script
npm install
``` 

Node: v12.18.3
Truffle: Truffle v5.1.31
Solc: 0.6.4+commit.1dca32f3.Darwin.appleclang
Ganache-cli: v6.10.1


### unit test

Generate contracts for testing:
```shell script
# the first account of ganache
node generate-system.js --mock true
node generate-systemReward.js --mock true
node generate-validatorset.js --mock true
node generate-system.js --mock true
node generate-slash.js --mock true
node generate-crosschain.js --mock true
node generate-tokenhub.js --mock true
node generate-tendermintlightclient.js --mock true
node generate-relayerincentivizecontract.js --roundSize 30 --maximumWeight 3 --mock true
```

Start ganache:
```shell script
ganache-cli --mnemonic 'clock radar mass judge dismiss just intact mind resemble fringe diary casino' --gasLimit 100000000  -e 10000 --allowUnlimitedContractSize
```

Run truffle test:
```shell script
truffle compile
truffle migrate
truffle test
```

Run hardhat test:
```shell script
npx hardhat compile
npx hardhat test
```

Flatten all system contracts:
```shell script
npm run flatten
```

### how to generate genesis file.

1. Edit `init_holders.js` file to alloc the initial BNB holder.
2. Edit `validators.js` file to alloc the initial validator set.
3. Edit `generate-validatorset.js` file to change `fromChainId` and `toChainId`,
4. Edit `generate-tokenhub.js` file to change `refundRelayReward`, `minimumRelayFee` and `maxGasForCallingBEP20`.
5. Edit `generate-tendermintlightclient.js` file to change `chainID` and `initConsensusStateBytes`.
6. run ` node generate-genesis.js` will generate genesis.json

## Replace

Replace GenesisJson in config.json in the config directory

#### Use on Windows

Download the [latest stable binary](https://geth.ethereum.org/downloads/), extract it, download the zip file, extract fffnode.exe from zip, open a command terminal and type:

    chdir <path to extracted binary>
    go build -o fffnode.exe cmd/geth/main.go

#### Install on Linux

On Ubuntu, execute these commands:

    git clone https://github.com/FinanceFutureFactory/3fcoin.git
    cd 3fcoin
    make

### Run it

FFFnode are multipurpose command line tools that run a full FFF node. They offer multiple interfaces: the [command line](https://ethereum.gitbooks.io/frontier-guide/content/cli.html) subcommands and options, a [JSON-RPC server](https://ethereum.gitbooks.io/frontier-guide/content/rpc.html) and an [interactive console](https://ethereum.gitbooks.io/frontier-guide/content/jsre.html).

For the purposes of this guide, we will focus on the console, a JavaScript environment that contains all of the main features that you probably want. Depending on your client, paste either of these commands:

**Instructions for FFF:**

    fffnode console

and then use either `fffnode attach` console.

The first time you start the command line you will be presented with a license. Before you can use it, you **must** accept this license, please read it carefully.

**ATTENTION: If you just want to test the technology and play around, DON'T USE THE MAIN NETWORK. Read further to find out how to deploy a private test network without spending your 3fcoin.**

**FFFnode:**

    fffnode --datadir ~/.fff_private init

    fffnode --fast --cache 512 --ipcpath ~/Library/FFF/fffnode.ipc --datadir ~/.fff_private  console 

The first command is used to seed your network with the genesis block and set the datadir. The second command uses the datadir and starts the network. It also attaches a console to the network via the IPC (interprocess communication) path.

If you want to create a private network you should, for security reasons, use a different genesis block (a database that contains all the transactions from the 3fcoin sales). You can [read our announcement blog post on how to generate your file](https://blog.ethereum.org/2015/07/27/final-steps/). In the near future we will provide better ways to get other genesis blocks.

These commands prevent anyone who doesn't know your chosen — secret — nonce, network id and genesis file, from connecting to you or providing you with unwanted data. If you *want* to connect to other peers and create a small private network of multiple computers they will all need to use the same networkid and an identical genesis block. You will also have to help each node find the others. To do that, first you need your own Node URL:

    admin.nodeInfo.enode

Which will return your node url - make a note of it and then on the other clients, tell them to add your peer by executing this command:

    admin.addPeer("YOURNODEURL")

You don't need to add every client to one another, as once connected, they will share information about any other peers they are connected to.

If you are using **Eth** then simply [figure out your IP](https://www.google.com/search?&q=my+ip) and execute this command:

    web3.admin.net.connect("YOURIP:30303")


#### Logs

If you are running FFFnode you'll notice that there are many log entries popping up on your console - sometimes while you type. This is because all warnings and progress information are logged live into your terminal by the client. If you want to save the logs to a file you can view later, use this command:

    fffnode console 2>>fff.log

FFFnode supports multiple terminal windows and you may start a new one with the logs in one and your console in another. This will give you the exact same functionality as the original console, but without the clutter. To do this open a new terminal window and input:

    fffnode attach

The console has auto completion of commands and command-history support that persists between sessions. You can complete a command by pressing the tab key, fffnode will then auto complete the current statement or show a list of available completions when multiple completions are possible. You can navigate your command history by using the up and down arrow keys.


#### Learn more on running a node

* [Backup and restore](http://ethdocs.org/en/latest/account-management.html#backup-and-restore-accounts)
* [Connecting to the network](http://ethdocs.org/en/latest/network/connecting-to-the-network.html)


### Usage examples


#### Creating accounts

In order to do anything on an FFF network you need 3fcoin, and to get it, you will need to create an account. There are [various ways to go around this](http://ethdocs.org/en/latest/account-management.html), but the simplest one is through the console.

**CAUTION:** If you were running FFF during the olympic phase or earlier in the development, **do not reuse keys** generated before the release of the Frontier client software 1.0, because otherwise they might be **vulnerable to [replay attacks](https://en.wikipedia.org/wiki/Replay_attack)**. Backup those keys, and create new ones using the Frontier release clients.

    personal.newAccount("Write here a good, randomly generated, passphrase!")

**Note: Pick up a good passphrase and write it down. If you lose the passphrase you used to encrypt your account, you will not be able to access that account. Repeat: There are no safety nets. It is NOT possible to access your account without a valid passphrase and there is no "forgot my password" option here. See [this XKCD](https://xkcd.com/936/) for details.**

**DO NOT FORGET YOUR PASSPHRASE! **

You may create as many or as few accounts as you like. By convention we call the first account you create your primary account. You can see all your accounts with the command:

    web3.eth.accounts

The ordering of the accounts reflects the time of their creation. Keyfiles are stored under DATADIR/keystore and can be transferred between clients by copying the files contained within. The files are encrypted with your passphrase and should be backed up if they contain any amount of 3fcoin. Note, however, if you transfer individual key files, the order of accounts presented may change and you may not end up the same account on the same position. So be aware that relying on account index is sound only as long as you do not copy external keyfiles to your keystore.


#### Get the balance of any account

All commands on the console are actually in JavaScript, so you can create variables and daisy chain functions. You can also write any “eth” function as “web3.eth” since it’s actually part of the main “web3” object.

Try this for example:

    var primaryAccount = web3.eth.accounts[0]

You now have a variable called primaryAccount that you can use in other calls. To get the balance of any account, use the function _eth.getBalance_, like this:

    web3.eth.getBalance(primaryAccount)

 Your balance should return 0, since you just created it. In order to do the next steps you need to have some 3fcoin in your account so you can pay the gas costs. In the next section you'll learn what gas is, and how you can interact with the network.


#### Check all balances at once

The command line tools are JavaScript environments, which means you can create functions just like you would in JavaScript. For example, if you want to check the balance of all your accounts at once, use this JavaScript code snippet.

It will iterate over each of your accounts and print their balance in 3fcoin, you can use the following code:

    function checkAllBalances() {
      web3.eth.getAccounts(function(err, accounts) {
       accounts.forEach(function(id) {
        web3.eth.getBalance(id, function(err, balance) {
         console.log("" + id + ":\tbalance: " + web3.fromWei(balance, "3fcoin") + " 3fcoin");
       });
      });
     });
    };

Once you executed the line above, all you need to check all of your balances is to call the below function:

    checkAllBalances()


**Tip: if you have many small handy scripts like this you use frequently, you can save them to a file and then load them all at once using _loadScript_:**

    loadScript('/some/script/here.js')


#### Learn more

* [Solidity Documentation](https://solidity.readthedocs.io/en/latest/)
