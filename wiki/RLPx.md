
### Goal
Facilitate multiple modes of communication over a generic transport, while also providing out-of-band control channels to both transport and sub-protocols. This provides an ideal protocol and groundwork for encrypted and multiplexed wireline communication which is robust and stochastic. Multiplexing is performed such that shared flows of data over the same connection cannot affect other flows anymore than what would be caused by normal network congestion.

### Overview
Messages are prefixed with variable RLP headers and delivered via multiplexed frames. RLPX adds flow control, wireline encryption, and sub-protocol channels.

#### Changes
* move packet-type out of payload RLP
* header inserted between payload-size and payload
* the baseline protocol reserves the first packet type for future control frames
* each sub-protocol may reserve it's first packet type for control frames
* multiplexing strategy

#### Why?
TL;DR - DApp networking, security, privacy, performance, and fairness

The Ethereum platform provides (and requires) networking which is diverse and spans nearly all layers of the network stack (link, network, transport, session, presentation, application). For some applications bandwidth is more important than latency, latency is critical for others, and some will not care. Ethereum runs these protocols alongside another over a single connection, so it's necessary that a protocol defines a simple interface and implementation which can facilitate fair, fast, and secure communication.

Typically, the implementation is not a critical part of a network protocol. However as a platform Ethereum is able to expose the network to DApps. Accordingly, a consistent implementation is necessary for applications to have a standard and reliable mechanism for interfacing securely with other DApps.

#### Why not SPDY?
SPDY only supports TCP, depends on TLS, is geared towards multiplexing HTTP browser streams, and uses fixed rather than variable-length fields. RLPX is based on RLP which is variable length, is expected to function seamlessly over UDP, and doesn't cater to HTTP. 

#### What RLPX is and isn't.
RLPX will not specify features beyond what is necessary for encryption and flow control. Some examples of this are routing, flow direction, prioritization, and multicast. Complex schemes can be negotiated and implemented via sub-prototocols. The initial version of RLPX will have artificial flow control and concurrency constraints which will later become dynamic parameters with recommended defaults based on the network environment (cpu, network interface, memory, operating system, etc.).

#### Why RLP?
RLP is compact, fast, high entropy, and well-suited for network applications built on Ethereum.

#### What an implementation provides
* connect to remote host, port, and public key
* multiple protocols over a single connection
* encryption
* in-band ciphertext authentication (future)
* multiplexing
* application-layer serialization
* forward-compatibility (control messages, encryption, channels)
* fast network processing

Example modes of communication:
* full-duplex messaging (aka, "push")
* multiplexed streams (concurrent transfers)
* concurrent request/response protocols (RPC)

#### Terminology
* Endpoint (aka Host): Optional. Responsible for lifecycle of connections and accepting ingress connections.
* Connection (aka Session): Mux/demux and encryption. May be registered with a service.
* Packet: Created by protocol, service, or application.
* Protocol: Implementation which processes and/or creates Messages. Protocols are registered-with and instantiated by a Service, instantiated and registered with a Connection, or, used in isolation.
* Service: A service may be registered to an endpoint and is able register itself with the connection to receive service-specific messages.

#### Recommended Reading
* [SPDY Protocol Spec V3](http://www.chromium.org/spdy/spdy-protocol/spdy-protocol-draft3-1)
* [The BitTorrent Protocol Specification](http://www.bittorrent.org/beps/bep_0003.html)
* [Queues Don't Fix Overload](http://ferd.ca/queues-don-t-fix-overload.html)
* [Amazon's Dynamo](http://www.allthingsdistributed.com/2007/10/amazons_dynamo.html)
