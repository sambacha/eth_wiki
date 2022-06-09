Ethereum (++) is the C++ proof-of-concept Ethereum command-line client. There is also a proof-of-concept Ethereum Qt-based client; you might want to see [[Using-AlethZero]].

Ethereum (++) can be executed by typing `eth`. It has several command-line switches to customise its behaviour, but in general it will attempt to connect to another peer or seed-node (they're the same thing, really), then become a functioning node in the network, collect transactions, blocks and peers and mining new blocks.

### Usage

    Usage eth [OPTIONS] <remote-host>
    Options:
    
    Client mode (default):
        --olympic  Use the Olympic (0.9) protocol.
        --frontier  Use the Frontier (1.0) protocol.
        --private <name>  Use a private chain.
        --genesis-json <file>  Import the genesis block information from the 
                               given json file.
    
        -o,--mode <full/peer>  Start a full node or a peer node (default: full).
        -i,--interactive  Enter interactive mode (default: non-interactive).
    
        -j,--json-rpc  Enable JSON-RPC server (default: off).
        --json-rpc-port <n>  Specify JSON-RPC server port (implies '-j',default: 8545).
        --admin <password>  Specify admin session key for JSON-RPC 
                            (default: auto-generated and printed at startup).
        -K,--kill  First kill the blockchain.
        -R,--rebuild  Rebuild the blockchain from the existing database.
        --rescue  Attempt to rescue a corrupt database.
    
        --import-presale <file>  Import a presale key; you'll need to type the password
                                 to this.
        -s,--import-secret <secret>  Import a secret key into the key store and use as 
                                     the default.
        -S,--import-session-secret <secret>  Import a secret key into the key store and
                                             use as the default for this session only.
        --sign-key <address>  Sign all transactions with the key of the given address.
        --session-sign-key <address>  Sign all transactions with the key of the given 
                                      address for this session only.
        --master <password>  Give the master password for the key store.
        --password <password>  Give a password for a private key.
        --sentinel <server>  Set the sentinel for reporting bad blocks or chain issues.
    
    Client transacting:
        --ask <wei>  Set the minimum ask gas price under which no transactions will be 
                     mined (default 500000000000).
        --bid <wei>  Set the bid gas price for to pay for transactions (default 
                     500000000000).
    
    Client mining:
        -a,--address <addr>  Set the coinbase (mining payout) address to addr (default:
                             auto).
        -m,--mining <on/off/number>  Enable mining, optionally for a specified number
                                     of blocks (default: off).
        -f,--force-mining  Mine even when there are no transactions to mine (default: 
                           off).
        --mine-on-wrong-chain  Mine even when we know it's the wrong chain (default: 
                               off).
        -C,--cpu  When mining, use the CPU.
        -G,--opencl  When mining use the GPU via OpenCL.
        --opencl-platform <n>  When mining using -G/--opencl use OpenCL platform n 
                              (default: 0).
        --opencl-device <n>  When mining using -G/--opencl use OpenCL device n 
                             (default: 0).
        -t, --mining-threads <n> Limit number of CPU/GPU miners to n (default: use 
                                 everything available on selected platform).
    
    Client networking:
        --client-name <name>  Add a name to your client's version string 
                              (default: blank).
        -b,--bootstrap  Connect to the default Ethereum peerserver.
        -x,--peers <number>  Attempt to connect to given number of peers (default: 5).
        --public-ip <ip>  Force public ip to given (default: auto).
        --listen-ip <ip>(:<port>)  Listen on the given IP for incoming connections 
                                   (default: 0.0.0.0).
        --listen <port>  Listen on the given port for incoming connections (default: 
                         30303).
        -r,--remote <host>(:<port>)  Connect to remote host (default: none).
        --port <port>  Connect to remote port (default: 30303).
        --network-id <n> Only connect to other hosts with this network id.
        --upnp <on/off>  Use UPnP for NAT (default: on).
        --no-discovery  Disable Node discovery.
        --pin  Only connect to required (trusted) peers.
        --hermit  Equivalent to --no-discovery --pin.
        --sociable  Forces discovery and no pinning.
    
    Work farming mode:
        -F,--farm <url>  Put into mining farm mode with the work server at URL 
                         (default: http://127.0.0.1:8545)
        --farm-recheck <n>  Leave n ms between checks for changed work (default: 500).
        --no-precompute  Don't precompute the next epoch's DAG.

    Ethash verify mode:
        -w,--check-pow <headerHash> <seedHash> <difficulty> <nonce>  Check PoW 
                         credentials for validity.
    
    Benchmarking mode:
        -M,--benchmark  Benchmark for mining and exit; use with --cpu and --opencl.
        --benchmark-warmup <seconds>  Set the duration of warm-up for the benchmark 
                                      tests (default: 3).
        --benchmark-trial <seconds>  Set the duration for each trial for the benchmark
                                     tests (default: 3).
        --benchmark-trials <n>  Set the duration of warm-up for the benchmark tests 
                                (default: 5).
        --phone-home <on/off>  When benchmarking, publish results (default: on).

    DAG creation mode:
        -D,--create-dag <number>  Create the DAG in preparation for mining on given 
                                  block and exit.

    Mining configuration:
        -C,--cpu  When mining, use the CPU.
        -G,--opencl  When mining use the GPU via OpenCL.
        --opencl-platform <n>  When mining using -G/--opencl use OpenCL platform n 
                               (default: 0).
        --opencl-device <n>  When mining using -G/--opencl use OpenCL device n 
                             (default: 0).
        -t, --mining-threads <n> Limit number of CPU/GPU miners to n (default: use 
                                 everything available on platform).
        --allow-opencl-cpu Allows CPU to be considered as an OpenCL device if the
                           OpenCL platform supports it.
        --list-devices List the detected OpenCL devices and exit.
        --current-block Let the miner know the current block number at configuration
                        time. Will help determine DAG size and required GPU memory.
        --cl-extragpu-mem Set the memory (in MB) you believe your GPU requires for 
                          stuff other than mining, like windows rendering e.t.c.
        --cl-local-work Set the OpenCL local work size. Default is 64.
        --cl-global-work Set the OpenCL global work size as a multiple of the local
                         work size. Default is 4096 * 64.
        --cl-ms-per-batch Set the OpenCL target milliseconds per batch (global 
                          workgroup size). Default is 0. If 0 is given then no 
                          auto-adjustment of global work size will happen.

    Client structured logging:
        --structured-logging  Enable structured logging (default output to stdout).
        --structured-logging-format <format>  Set the structured logging time format.
        --structured-logging-url <URL>  Set the structured logging destination 
                                        (currently only file:// supported).

    Import/export modes:
        -I,--import <file>  Import file as a concatenated series of blocks and exit.
        -E,--export <file>  Export file as a concatenated series of blocks and exit.
        --from <n>  Export only from block n; n may be a decimal, a '0x' prefixed hash,
                    or 'latest'.
        --to <n>  Export only to block n (inclusive); n may be a decimal, a '0x'
                  prefixed hash, or 'latest'.
        --only <n>  Equivalent to --export-from n --export-to n.
        --dont-check  Avoids checking some of the aspects of blocks. Faster importing,
                      but only do if you know the data is valid.
    
    General Options:
        -d,--db-path <path>  Load database from path (default: '~/.ethereum',
                             '<APPDATA>/Etherum' or 'Library/Application Support
                             /Ethereum').
        --vm <vm-kind>  Select VM. Options are: interpreter, jit, smart 
                        (default: interpreter).
        -v,--verbosity <0 - 9>  Set the log verbosity from 0 to 9 (default: 8).
        -V,--version  Show the version and exit.
        -h,--help  Show this help message and exit.
    
    Experimental / Proof of Concept:
        --shh  Enable Whisper.

### Starting a Peer Server

```
/path/to/eth -m off -o peer -u 65.78.90.42 -x 256
```

This starts the node as a peer-server with internet-visible IP 65.78.90.42, able to accept up to 256 peers and share connection information between them. As more than 256 peers get connected, the older peers (that have had a chance to gather peer information of their own) will get disconnected to make way for the newer crowd.

```
/path/to/eth -u 192.168.0.5 -p 30301 192.168.0.10
```

This starts a full node on the local client (whose IP is 192.168.0.5) and attempts to connect the LAN peer 192.168.0.10 on port 30301.

```
cd /tmp
mkdir client1
/path/to/eth -d client1 -u 127.0.0.1 -l 30303 &
mkdir client2
/path/to/eth -d client2 -u 127.0.0.1 -l 30300 -p 30303 127.0.0.1
```

This creates two full clients on the same host, possible because the databases are stored in different paths, and connects them together. One listens on the local host on port 30303, and the other on 30300.