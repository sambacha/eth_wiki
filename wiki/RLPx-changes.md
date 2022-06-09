
Changes to Ethereum wireline protocol, as required to support the RLPx protocol.

## PoC-0
### Overview
* support for multiple protocols
* signed handshake
* peer discovery
* foward-compatible framing

#### Well-formed Network
* peer discovery -- new nodes can reliably find nodes to connect to

#### Transport
* multiple p2p protocols over a single connection
* signed handshake


### Implementation
Packets are prefixed with an RLP-encapsulated frame header. The initial handshake is signed and authenticated.

PoC-0 RLPx is composed of:
* Signed Handshake
* Framing & Multiplexing
* Node Discovery & Peer Preference

#### Changes from current network protocol
* replace sync-token with header (basic version: frame-size || [protocol-type])
* insert frame header between payload-size and payload
* implement encrypted handshake
* protocol type is sent in frame header and packet-type is no longer offset
* kademlia-like peer discovery

#### PoC-0: Encrypted Handshake
*refer to rlpx.pdf*

#### PoC-0: Framing & Multiplexing (excludes handshake)
Packets sent over the wire are encapsulated as frames.
```
single-frame packet: header || frame
        header: frame-size || header-rlp
                frame-size: 3-byte integer size of frame (size excludes header-rlp, big endian encoded)
                header-rlp: rlp.list(protocol-type[, sequence-id])
                    values:
                        protocol-type: < 2**16
                        sequence-id: < 2**16 (this value is optional for normal frames)
        frame: rlp(packet-type)[ || rlp(packet-data)]
```

Notes:
* || is concatenate
* [] is optional

#### PoC-0: Node Discovery & Peer Preference
* Node: Any peer on the network.
* Peer: Node which is currently connected to local node.
* Id of node/peer is their public key
* kademlia-like protocol

The kademlia protocol is implemented with signed messages, a bucket size _k_ of 16 (_Note that bucket size needs to be maintained separately for each supported protocol; currently Ethereum base protocol and Swarm. Bucket size might be different for different protocols. Peers supporting multiple protocols count towards each supported protocol's bucket size._), concurrency (alpha) of 3, and 8 bits per hop for routing. The eviction check interval is 75 milliseconds, request timeout is 300ms, and idle bucket-refresh interval is 3600 seconds.
```
NodeTable::join() -> Called by new peer to join network
NodeTable::nodes() -> Returns Ids of nodes in node table.
<class T> NodeTable::peers() -> Returns list of peers which are connected to facilitate protocol implemented by class T.

Packets:
struct PingNode
{
        std::string ipAddress; // our IP
        unsigned port; // our port
        unsigned expiration;
};
struct Pong  // response to PingNode
{
        h256 replyTo; // hash of rlp of PingNode
        unsigned expiration;
};
struct FindNode
{
        NodeId target; // Id of a node. The responding node will send back nodes closest to the target.
        unsigned expiration;
};
struct Neighbors  // reponse to FindNode
{
        struct Node
        {
                std::string ipAddress;
                unsigned port;
                NodeId node;
        };

        std::list<Node> nodes;
        unsigned expiration;
};

NodeId is the node's public key.
```