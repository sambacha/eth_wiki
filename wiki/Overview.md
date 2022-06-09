The library code exists as several separate projects:

* Fundamental ÐΞV-software, namespace `dev`:
  - **libdevcore** Core components used by Peer library, Ethereum, Whisper and Swarm along with other related projects. Only depends on Boost.
  - **libdevcrypto** Core crypto components used by Peer library, Ethereum, Whisper and Swarm along with other related projects. Includes RLP and the Merkle Patricia tree. Depends on libsecp256k1 and libcrypto++.

* **libp2p**: ÐΞV Peer networking framework, namespace `dev::p2p`.

* **libwhisper**: The Whisper software, namespace `dev::shh`.

* **libswarm**: The Swarm software, namespace `dev::bzz`.

* Ethereum software, namespace `dev::eth`, depends on libdev* and libp2p.
  - **libethcore** Core interfaces and components.
  - **libevmface** The Ethereum Virtual Machine interface.
  - **liblll** The Low-level LISP-like Language compiler & assembler.
  - **libevm** The Ethereum Virtual Machine implementation.
  - **libethereum** Class library that forms a complete implementation of an Ethereum node. Includes the State and BlockChain classes.
  - **libqethereum** Classes to provide Qt Javascript and QML bindings to the Ethereum API.
  - **libserpent** Vitalik's C++ Serpent implementation.

* **libwebthree**: Amalgamation of all projects, and includes a DB multiplexing class. Namespace `dev`.

In addition to these libraries, a set of third-party libraries are also distributed alongside the core libraries including:

- **secp256k1** An implementation of the SECP 256k1 ECDSA signing algorithm.
- **json_spirit** A C++ JSON parser written for Boost's Spirit library.

The core executable projects within cpp-ethereum are four:

- **eth** A command-line Ethereum server and shell able to be a full-node on the Ethereum network.
- **alethzero** A Qt-based all-encompassing GUI for interacting with Ethereum.
- **third** A basic Ethereum browser.
- **neth** An NCurses-based command line UI for Ethereum.

A number of additional executables also exist for testing and development:

- **test** A set of unit tests for the Ethereum libraries.
- **exp** Experimental code.
- **lllc** The LLL command-line compiler.
- **sc** The Serpent command-line compiler.

There are additional support directories:
- **extdep** For managing library dependencies on Windows
- **docker** For building using docker.