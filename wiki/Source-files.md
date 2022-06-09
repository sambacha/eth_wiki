# List of directories and what files do
Note that this is both rough, and may not be updated wel enough.

## libevm/
The Ethereum Virtual Machine.

#### VM
Implements the actual Virtual machine.

#### ExtEvmFace
Defines a class that the EVM talks too, but doesnt actually implement it.
'null implementation'.

#### FeeStructure
Defines what the fees for different operations are.

## libethcore/
Shared, core components to the Ethereum and (potentially) other related projects.
Includes RLP and the Merkle Patricia tree.
 
### .cpp and .h files here:

#### Dagger
Algorithm to mine and verify the 'mined bit' of the blocks.

#### BlockInfo
Reading a block RLP data, verifying internals; whether accounts have enough
ether to pay for the sent ethers plus maximum possible gas cost, aswel as
checking for the global gas limits, uncles, etcetera.

NOTE: actually *dont* see it check the value+gas cost thing..

#### CommonEth
Contains the private/public `KeyPair`, and the different units of ether.

Apparently no verify or sign function on the `KeyPair`, possibly the function
is used using the public/private key directly.

#### Instructions
Contains a list of all the instructions names and bytes, and `eth::disassemble`
that turns the bytes into a more human readable string.(although not *very*
human readable, being able to recompile higher would be a nice feature.)

(currently no `eth::assemble` matching `eth::disassemble`)

#### Exceptions
Because we all love throwing stuff.

## libethsupport/

### .cpp and .h files

#### RLP
Implements the [RLP](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP).
RLP achieves defining a way to indicating nested lists with blocks of data in
them. (Something else looks at the blocks to see how to use it)

#### TrieDB, TrieCommon
Implements the [Patricia tree](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree)
thing. (or [here](http://easythereentropy.wordpress.com/2014/06/04/understanding-the-ethereum-trie/))

#### FixedHash

#### OverlayDB

#### MemoryDB

#### UPnP

#### Log
Handles logging things.

#### Common
Defines some arrays and sets with names that are easier to type and read, as
well as some other simple definitions.

## eth/

#### EthStubServer
Functions that do things with string input, used for JSON rpc commands.