This document serves as a quick guide to doing client development on the Ethereum C++ PoC-6 codebase. It is for those who wish to interface to the PoC-6 testnet for the purposes of submitting transactions, mining blocks and querying state. Ethereum is still in the very early stages of development and as such the code, protocol and API are changing speedily and in significant ways. In short, here be dragons.

### Basics

To utilise the Ethereum network for your application you must create a new Ethereum client. Though a non-trivial undertaking, it is fairly streamlined.

To use any of this, you're going to need a development build of cpp-ethereum (see github.com/ethereum/cpp-ethereum and it's wiki for how to do that). Once built, you'll end up with libethereum and a bunch of clients. You'll be using libethereum. 

The Ethereum library, libethereum, gives you all the APIs you need to develop a new Ethereum client, however for ease of development, a single class is provided in order to streamline your path to a useful client, called, surprisingly enough, `Client`.

Find the header at libethereum/Client.h. You'll need to link your code to libethereum.so (or .dylib, .dll depending on your platform and build settings).

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
client.startNetwork(30303, "54.76.56.74", 30303);
```

This call puts us on the Ethereum peer network. We have to specify the port on which we want to listen (the first `30303`), the initial peer to which we want to connect (`"54.76.56.74"`, the PoC-6 testnet nexus, but any other peer will do if you know their address), the port to connect on (the second `30303`). There are  also bunch of parameters about how our peer behaves which we leave at the defaults; in this case we want a full peer, maintaining ideally 5 connections and we don't know its public IP but we'll use UPnP to determine that and set up any port forwarding (this will make this call block for a second or two).

Once made, a second thread will service the network; you need to do nothing.

### Querying state

So we use the client class to query state of the network. Suppose we want to check our balance (of the account we created earlier, stored in `me`):

```
eth::u256 myBalance = client.balanceAt(me.address());
if (myBalance > 0)
{}   // Got Ether! :-)
else
{}   // Still Wei-less. :-(
```

In this case we just use the `Client::balanceAt()` method. Easy.

Suppose we have a contract address (`myContract`, let's say) and we wish to query what is stored at address index `3`:

```
eth::Address myContract;
// ...
eth::u256 at3 = client.stateAt(myContract, 3);
```

or perhaps we know that there's something interesting to us stored at our address:

```
eth::u256 atMyAddress = client.stateAt(myContract, me.address());
```

### Learning of Changes

Ethereum includes a powerful mechanism for determining when the state of the system changes, either because a transaction is submitted or because somebody mined something. At present, the system is based around a polling mechanism and 'watch'es; watches are triggered on particular changes to the system state and can be queried (in a thread-safe manner) whether the state has since changed. Watches can be created with the `Watch` class:

```
Watch chainChanged(client, ChainChangedFilter);
```

This creates a watch which _watches_ (geddit?) for whenever the blockchain changes (i.e. when a new block is mined or the chain has altered in some other way). In addition to the ChainChangedFilter, there is another inbuilt filter for watching the pending transaction list: PendingChangedFilter. It works in the same way.

The watch can be queried with `check`:

```
if (chainChanged.check())
  cout << "The chain has changed!" << endl;
```

Calling `check` resets the status of the watch (calling it twice in a row will generally result in the second returning false). If you don't want to reset the status, use `peek`.

Watches are non-copyable by design, though may be swapped (there is a std::swap method for them).

While determining when the chain changes is an important ability, it is rather a fine net and tends to catch everything. There are ways of filtering in a much more detailed way avoiding being notified of uninteresting changes. This is done with `MessageFilter`s.

### Filtering

A `MessageFilter` specifies a set of conditions against which messages (i.e. calls and contract creations) can be matched against.

To use a message filter, you construct the object and then narrow-down the types of messages you wish to know about. For example, suppose we wish to be notified of messages that alter our account:

```
Watch myAccountWatch(client, MessageFilter().altered(me.address()));
```

There are a number of other ways of narrowing-down the messages that we might want to be notified of. `MessageFilter::from` and `::to` allow us to filter based on the source and receipt addresses. `MessageFilter::altered(addr, loc)` allows to to filter based on a particular contract's point in storage changing.

### Gathering Messages

Once notified of changes, it is often useful to know the precise nature of those changes. As such, `MessageFilter`s have another purpose; namely to gather these messages and allow them to be inspected. Once a `Watch` is set up, you may call `messages` to return you a number of `PastMessage` objects. This is an ordered list, each one describing a particular message that was sent within Ethereum.

```
for (PastMessage const& m: myWatchAccount.messages())
  cout << "I was altered by a message from " << m.from << " in block " << m.number << endl;
```

The `PastMessage` object has a number of fields of information. Particularly useful are the `input` and `output` fields, which can allow a contract to provide useful information to its operation to you, the querier. There is also `to`, `from` and `value`, together with `path` giving you insight into where in the block's message tree it is. There are also several fields which describe the block in which the message was active.

Aside from using watches to rake in `PastMessage`s, you may also use the `Client` directly with the `messages` call. As might be expected, this takes a `MessageFilter` object and returns messages. This is particularly useful when combined with the `MessageFilter`'s ability to match only against a particular set of blocks on the blockchain (see `withEarliest` and `withLatest`).

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
std::assert (client.balanceAt(me) == myBalance + 100 * eth::finney);
```

And that's it. Any questions?

Gav.