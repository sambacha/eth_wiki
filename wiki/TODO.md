### Very soon

- Full state unit tests. [Christoph]

### Soon

Cleanups & caching
- All caches should flush unused data (I'm looking at you, BlockChain) to avoid memory overload. 
- State DB should keep only last few N blocks worth of nodes (except for restore points - configurable, defaults to every 30000th block - all blocks that are restore points should be stored so their stateRoots are known good).
- Logger: cleanup windows-specific code, determine impact of Release vs Debug, settings

Trie on DB.
- Move the restore point stuff into block restore points
  - i.e. keep all nodes from last 127 blocks with counter, at 128, kill but keep every (60*24*7)th or so i.e. one per week as a restore point.
  - maybe allow this to be configured.

Robustness
- Remove aborts
- Recover from all exceptions.
  - Especially RLP & other I/O.
- Better handling of corrupt blocks.
  - Kill DB & restart.

### Later

GUI
- Make address/block chain list model-based, JIT populated.
- Make everything else model-based.
- Qt/QML class.

Thread-Safety
- State class?

Crypto stuff:
- kFromMessage

Network:
- *** Exponential backoff on bad connection.
- Better handling of bad blocks
  - Track which peers passed which blocks and punish them.
  - Don't pass on block until they're known good.
- Crypto on network - use id as public key?
  - Also have system of passing public key (or portion thereof) with the network. Base-58 encoded.
  - Also have format of passing public key/IP/port/checksum as single base-58 string.
- Make work with IPv6
- Peers rated.
  - Useful/useless - new blocks/transactions or useful peers?
  - Solid communications?
- Strategy for peer suggestion?

General:
- Better logging.
  - Colours.
  - Move over to new system.

GUI:
- Turn on/off debug channels.
