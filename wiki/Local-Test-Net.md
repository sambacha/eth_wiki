Setting up a local test network in Ethereum is easy. For this you'll need at least two computers on the same network, both the Ethereum software built. You'll need to know their IP addresses. You can find these by running `ifconfig` in a terminal and checking the output.

Firstly we'll set up a command-line client; in a terminal on one machine. We'll assume for the present that this machine has the IP address 192.168.0.10:

```
cd /path/to/cpp-ethereum-build/eth
./eth -u 192.168.0.10 -l 30300 -d /tmp
```

This will create an Ethereum node with the database in the `/tmp` path and listening for connections on port 30300.

Now we want to connect to that with the second machine (let's suppose it's at address 192.168.0.11). Run AlethZero:

```
cd /path/to/cpp-ethereum-build/alethzero
./alethzero
```

And then click the 'Connect' button. In the ensuing window, you can type the address of your local node (which we're assuming is 192.168.0.10) rather than the global node. You'll also need to give it the port on which your local node is accepting connections (it's the default of 30300); so in this example, you would type in:

```
192.168.0.10:30300
```

And hit 'OK'.

If all goes well, you should see a message indicting a new connection in the terminal on the original computer and a single entry in the first pane on your AlethZero client on the second computer. Click the 'Mine' button to begin mining and making some ether!

You can connect to the node with the original machine by running AlethZero in the same way on that machine. We can run two clients on it since we're making sure one of them has it's database in a separate place to the other (that was the point of having `-d /tmp`).

Once you have the node running and connected (the process is equivalent), you can begin mining.

### Sending Ether

Having mined (in order to make some Ether), you can send some Ether from one computer to another simply by typing in the amount you wish to send. Copy the address of the recipient client (the long hexadecimal string at the bottom of the window) into the recipient box of the sending client (the text box at the top of the window, just before the 'Send' button). Then enter the amount you wish to send and the fee you wish to pay in the two boxes next door and click 'Send'. You'll see the transaction move between the clients, and after the next two blocks are mined (it won't be included in the first as it's already being mined) it will be in the block-chain (the fourth panel).

### Running a Contract

In this version, you can't easily run a contract from the GUI. But it's coming soon!

