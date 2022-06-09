### Use case

In general, think static web-page and digital asset downloads. Like FTP but with files specified by hash rather than by server & path.

### Specs

Reverse hash-lookup (given hash it finds the data) with:

* **Low-ish latency** Lowest likely download rate of asset should be reasonable, even (especially) for assets of < 1MB.
* **High throughput** Average download rate of asset should be minimal.
* **Private** Plausible deniability against data publication or subscription.
* **Lossy** No implicit guarantee that other nodes will maintain data.

### Existing solutions

* [BitTorrent](http://www.bittorrent.org/beps/bep_0005.html)
* BitSwap (IPFS) (http://ipfs.io)

### Basic Design

Uses the `"bzz"` protocol string of ÐΞVP2P.

Rest coming soon, once I've finished prototyping. Gav.


Base it off of IPFS. (http://ipfs.io) Don't reinvent the wheel. -Siraj