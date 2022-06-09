This document serves as a quick guide to doing client development on the Ethereum C++ PoC-4 codebase. It is for those who wish to interface to the PoC-4 testnet for the purposes of submitting transactions, mining blocks and querying state. Ethereum is still in the very early stages of development and as such the code, protocol and API are changing speedily and in significant ways. In short, here be dragons.

### Basics

To utilise the Ethereum network for your application you must create a new Ethereum client. Though a non-trivial undertaking, it is fairly streamlined. As of PoC-4 there is no "node server", JSON-RPC interface or other such multiplexing mechanism.

To use any of this, you're going to need a development build of cpp-ethereum (see github.com/ethereum/cpp-ethereum and it's wiki for how to do that). Once built, you'll end up with libethereum and a bunch of clients. You'll be using libethereum. 

The Ethereum library, libethereum, gives you all the APIs you need to develop a new Ethereum client, however for ease of development, a single class is provided in order to streamline your path to a useful client, called, surprisingly enough, `Client`.

Find the header at libethereum/Client.h. You'll need to link your code to libethereum.a (or .so, .dylib, .lib, .dll depending on your platform and build settings).

### Client class overview

The client class provides you with the ability to become a peer on the Ethereum network (via `startNetwork()` method), to mine blocks (`startMine()`, though if you're just wanting to transact and query state you can ignore this), to transact (the `transact()` methods) and to query the state (the `state()` method).

The basic usage of the Client class involves first creating an instance:

```
eth::KeyPair me = eth::KeyPair::create();
eth::Client client("My App", me.address(), "./MyApp");
```

This creates a client `client` whose network ID is "My App" and which stores the various disk-backed databases in the directory "./MyApp" (relative to the working directory of the application). You'll probably want to use a more permanently known path than the current directory; `eth::getDataDir()` might help you there.

If the client does any mining, any proceeds will be placed in the account identified by our newly created keypair `me`. Using this code, it'd change each time you run the application. Hardly ideal. You'd want to generate it once and use that one generated address throughout.

Creating this instance initialises the databases (if they don't already exist), but does little else. We're not on the network yet! To connect to the network, we use `startNetwork()`:

```
client.startNetwork(30303, "54.72.31.55", 30303);
```

This call puts us on the Ethereum peer network. We have to specify the port on which we want to listen (the first `30303`), the initial peer to which we want to connect (`"54.72.31.55"`, the PoC-4 testnet nexus, but any other peer will do if you know their address), the port to connect on (the second `30303`). There are  also bunch of parameters about how our peer behaves which we leave at the defaults; in this case we want a full peer, maintaining ideally 5 connections and we don't know its public IP but we'll use UPnP to determine that and set up any port forwarding (this will make this call block for a second or two).

Once made, a second thread will service the network; you need to do nothing.

### Querying state

So to query state of the network we use the `State` class. It's quite complex, but for just querying information, the usage is pretty straightforward. Suppose we want to check our balance (of the account we created earlier, stored in `me`):

```
eth::u256 myBalance = client.state().balance(me.address());
if (myBalance > 0)
{}   // Got Ether! :-)
else
{}   // Still Wei-less. :-(
```

In this case we just use the `State::balance()` method and get the state from the client with the `Client::state()` method. Easy.

Suppose we have a contract address (`myContract`, let's say) and we wish to query what is stored at address index `3`:

```
eth::Address myContract;
// ...
eth::u256 at3 = client.state().contractStorage(myContract, 3);
```

or perhaps we know that there's something interesting to us stored at our address:

```
eth::u256 atMyAddress = client.state().contractStorage(myContract, me.address());
```

Note: There is not currently any method to be notified of state changes on the network. You're just going to have to poll for the time being. This will be integrated by the time PoC-6 hits the streets.

### Transacting

To send a transaction onto the network, there are two `Client::transact()` methods; one for creating new contracts and one for creating message-calls. Creating contracts is probably not the sort of thing you want to do programatically yet; you'll probably just use AlethZero.

Suppose we wished to send 10 Ether to the Bob, whose address we happen to know is stored in the variable `bob`. Then we would call:

```
client.transact(me.secret(), 10 * eth::ether, bob);
```

It's that easy.

Suppose I want to send a message to a contract, which, again, I happen to know is stored in `myContract`. Suppose the message I want to send are the two bytes `0x4269` and let's say I don't want to send any Ether with it:

```
client.transact(me.secret(), 0, myContract, { 0x42, 0x69 });
```

Again, that simple.

### Creating Contracts

Finally, let's assume I want to make a new contract. I want it to be a simple cash collection contract. I create it, and when I send a message to it, it suicides, giving me all its balance. 

Firstly, let's figure out what the contract's code would be. We'll write it in LLL, since a compiler for this is included in libethereum. For the contract's initialisation, it should remember who its creator is (since we'll be returning the cash to them later) and store it away (let's say at position 69):

```
[[69]] (caller)
```

For the body, it should check to see if the transaction is coming from the creator; if so, it should terminate and return the cash to them:

```
(when (= (caller) @@69) (suicide (caller)))
```

Not too difficult either.

So now we need the two byte arrays that correspond to these. We compile them with the `eth::compileLisp()` function, found in the header Instruction.h:

```
eth::bytes initCode = eth::compileLisp("[[69]] (caller)");
eth::bytes bodyCode = eth::compileLisp("(when (= (caller) @@69) (suicide (caller)))");
```

We then create the contract using this data. We won't endow it with any Ether:

```
eth::Address donations = client.transact(me.secret(), 0, bodyCode, initCode);
```

Now that's done, anyone will be able to donate to it. Suppose Alice comes along, who has her own keypair `alice` and she wants to donate 100 Finney. Then she would call:

```
client.transact(alice.secret(), 100 * eth::finney, donations);
```

On receipt it would check if the caller (Alice) is the same as the original creator (me). It's not, and so it would accept the funds and do nothing more.

Now, suppose we want to empty the pot. We send an empty message to it signalling it to suicide:

```
client.transact(me.secret(), 0, donations);
```

This will run the same code as before, but this time, the caller is the same as the original creator and so it would suicide, depositing the entire balance (which is apparently just Alice's measly 100 Finney). And so, all other things being equal, we should be able to assert that we have 100 Finney more than we had earlier (stored in `myBalance`):

```
std::assert (client.state().balance(me) == myBalance + 100 * eth::finney);
```

And that's it. Any questions?

Gav.