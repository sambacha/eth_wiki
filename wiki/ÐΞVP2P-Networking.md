NOTE: See [Wire Protocol](https://github.com/ethereum/wiki/wiki/ÐΞVp2p-Wire-Protocol) for the current protocol definition.

### Goals

- Allow multiple protocols to leverage the same peer network & messaging system.
- Maximise code reuse between protocols.

### Basic Overview

Rather than having multiple peer networks for each of the different P2P protocols (currently Ethereum, Whisper and Swarm but with the potential for there to be several others), we have a single P2P mother protocol forming the spine of the network. This is then able to support multiple mutually-independent sub-protocols within its framework.

Thus we split off the basic P2P networking portion of the protocol from the Ethereum-specific portion. For now we manually reserve chunks of message-ID space. In a later revision of this protocol, we will have a dynamically negotiating message-ID scheme. For now, message IDs `0x00` to `0x0f` are reserved for specifically P2P stuff. Message IDs `0x10` to `0x1f` will be used by the `eth` protocol (Ethereum). Message IDs `0x20` to `0x2f` will be used by the `shh` protocol (Whisper).

### Required Changes

- `0x00` `Hello`: `[0x00: P, p2pVersion: P, clientVersion: B, [cap0: B, cap1: B, ...], listenPort: P, id: B_64]`
  - `p2pVersion`: The implemented version of the P2P protocol. `0`
  - `clientVersion`: The underlying client. A user-readable string.
  - `capN`: A peer-network capability code, readable ASCII and 3 letters. Currently only `"eth"` and `"shh"` are known.
  - `listenPort`: The port on which the peer is listening for an incoming connection.
  - `id`: The identity and public key of the peer.
- `0x01` `Disconnect`: Unchanged except for reason list.
  - Disconnect reason "incompatible genesis block" is removed.
- `0x02` `Ping`: Unchanged.
- `0x03` `Pong`: Unchanged.
- `0x04` `GetPeers`: Unchanged.
- `0x05` `Peers`: Unchanged.

Clients that are able and willing (i.e. not simply peer-servers) to take part in the Ethereum network should include the `"eth"` capability and recognise the following packets:
- `0x10` `Status`: `[0x10: P, protocolVersion: P, networkID: P, totalDifficulty: P, latestHash: B_32, genesisHash: B_32]`
  - `protocolVersion`: The version of the Ethereum protocol this peer implements. `33` at present.
  - `networkID`: The network version of Ethereum for this peer. `0` for the official testnet.
  - `totalDifficulty`: Total difficulty of the best chain. Integer, as found in block header.
  - `latestHash`: The hash of the latest block in our canonical chain, or alternatively put, the block with the highest validated total difficulty.
  - `genesisHash`: The hash of the Genesis block.
- `0x11` `GetTransactions`: Unchanged.
- `0x12` `Transactions`: Unchanged.
- `0x13` `GetBlockHashes`: Unchanged.
- `0x14` `BlockHashes`: Unchanged.
- `0x15` `GetBlocks`: Unchanged.
- `0x16` `Blocks`: Unchanged.

`Status` should be sent immediately after the `Hello` message exchange has demonstrated both peers have the `eth` capability.