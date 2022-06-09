For PoC-7 and onwards into the Release Series, we'll use a more expansive namespace system. To avoid confusion between the three sibling projects of Ethereum, Whisper and Swarm, the root namespace of all C++ code will now be altered to `dev`.

- `dev` libdevcore, libdevcrypto, libwebthree: All low-level utilities and shared classes, together with the Web Three library that consolidates Ethereum, Whisper and Swarm.
- `dev::p2p` libp2p: The low-level peer-to-peer networking framework.
- `dev::eth` libethcore, liblll, libevmface, libevm, libethereum: All Ethereum-specific code.
- `dev::shh` libwhisper: The Whisper library.
- `dev::bzz` libswarm: The Swarm library.
