AlethZero is the C++/Qt proof-of-concept Ethereum graphical client.

It can be used to connect the global Ethereum test network (by simply running it and clicking 'Connect', followed by 'OK'), but can also be used to connect to a local test network.

![Based on commit 4fde0bd991](http://i.imgur.com/1jho0wN.png)

AlethZero has seven panes; 

* **Transact** (Top left): For making transactions
* **Owned Accounts** (Middle left): List of your Ethereum addresses
* **Log** (Bottom): Displays details about the activity of your client
* **Blocks & Transactions** (Middle): List of all blocks on the network. If they contain transactions, these will be listed below the block#
* **All Accounts** (Top right): List of all known accounts that contain 'ether' on the network
* **Network** (Middle right): Shows a list of all the nodes you are connected to
* **Pending** (Bottom right): All the transactions on the network that have not been included in a block

The statusbar at the bottom of AlethZero shows your total account balance, number of peers and some information about the block chain size and difficulty. You can also watch video instructions [YouTube: Joel's First Time Using the Ethereum AlethZero Client](https://www.youtube.com/watch?v=vXGH6q43i_k) for version v3.11

Interaction with AlethZero happens on the toolbar along the top; the 'Connect' button is used to begin the network subsystem and start listening for connections. To make your first connection into the network, enter the details of the peer you wish to connect to, or, if you want to connect to the global test network, leave it as it is and click 'OK'.

You can send funds (once you have some!) by entering the amount of funds to send, an appropriate fee and the address to which you wish to send them.  Once this is done, clicking Send will package that transaction up and put it into the transaction queue.

## Mining

To enlarge the block chain and "set in stone" transactions, people must mine in Ethereum. In AlethZero you can do this simply by clicking the 'Mine' button. After a while (it could take a long time!), you should see blocks appearing - these have been "mined" and will be sent to any peers that are connected. If they mine one first, you'll see it listed in the column.

That's it - happy mining!

## Getting test Ether

To make any transaction or create a contract, you need Ether. You can get test ether from the [wei faucet](https://zerogox.com/ethereum/wei_faucet) like this:

1. Copy your ethereum address by double clicking on it
1. Paste it in the wei faucet and press "Gimme wei"
1. A pending transaction to your address appears in AlethZero
1. When this transaction is processed in a block (either mined by yourself or another peer on the network) the funds are added to your address.

## Registering your name

To turn addresses like "0x5d1be2e04098d5e7dbc21c691e1599390ca94b8c" into readable names like "MyEthereumAddress", a [default contract called NameReg](https://github.com/ethereum/cpp-ethereum/wiki/Name-Registration-API) is available. Names registered in NameReg are shown in AlethZero, Mist and other Ethereum clients.

When you have some test ether, you can make your first transaction. Why not register your name? Submit a transaction like this:

* To: NameReg
* Amount: 0
* Gas: 10000 gas @ 10 szabo
* Data: "register" "MyEthereumAddress"

Press 'Execute' to submit the transaction. The transaction will show up under "Pending", then when mined it will show up as a transaction in the Block Chain. After this, you should see "MyEthereumAddress" anywhere your address is displayed.

## Using multiple addresses

In case you don't have the address of a friend to send them, you can create new addresses. This allows you to test your DApps as if there were multiple users, using a single instance of AlethZero.

Create a new address by clicking the 'Create' from the Tools-menu. This will give you a new address along with the secret key that allows you to sign transactions. Press the 'Refresh' button to see your new address, or it will only be visible after a restart.

AlethZero will use the address at the top of list when submitting new transactions and as the coinbase for mining. To change the order of the addresses, drag and drop them.