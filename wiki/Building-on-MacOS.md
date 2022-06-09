# THE PROCESS DESCRIBED HERE IS OUTDATED. PLEASE STANDBY WHILE WE UPDATE OUR DOCUMENTATION.

Although Mavericks is required for compiling the app, the app has been tested to run on 10.8, 10.9 and 10.10. 

## Build with Homebrew

For the stable branch:
```
brew tap ethereum/ethereum
brew install cpp-ethereum
brew linkapps cpp-ethereum
```

For the development branch:
```
brew reinstall cpp-ethereum --devel
brew linkapps cpp-ethereum
```

Add the `--with-gui` option to also build AlethZero and the Mix IDE.

Then `open /Applications/AlethZero.app`, `eth` (CLI) or `neth` (ncurses interface)

For options and patches, see: https://github.com/ethereum/homebrew-ethereum

## Manual Build

### Prerequisites
* Latest Xcode on Mavericks (at least Xcode 5.1+).
* Homebrew package manager  http://brew.sh *or* MacPorts package manager  http://macports.org
* XQuartz  http://xquartz.macosforge.org/landing/

### Install dependencies

Using homebrew:

    brew install boost --c++11 # this takes a while
    brew install boost-python --c++11 # optional, for serpent support
    brew install cmake qt5 cryptopp miniupnpc leveldb gmp libmicrohttpd libjson-rpc-cpp
    brew install homebrew/versions/v8-315
    brew install llvm --HEAD --with-clang

Or, using MacPorts:

    port install boost +universal # this takes a while
    port install cmake qt5-mac libcryptopp miniupnpc leveldb gmp rocksdb v8
    (probably missing libmicrohttpd and libjson-rpc-cpp)

### Clone source repo and create build folder
    git clone https://github.com/ethereum/cpp-ethereum-cmake
    git clone https://github.com/ethereum/cpp-ethereum.git
    cd cpp-ethereum
    mkdir -p build # create build folder ('build' ignored by git)

### Compile 
    cd build
    LLVM_DIR=/usr/local/Cellar/llvm/HEAD/share/llvm/cmake cmake ..
    # or, if using MacPorts, just cmake ..
    make -j6
    make install

This will also install the cli tool and libs into /usr/local.

### XCode Instructions
From the project root:
```
mkdir build_xc
cd build_xc
cmake -G Xcode ..
```
This will generate an xcode project file along with some configs for you: ethereum.xcodeproj . Open this file in XCode and you should be able to build the project

## Troubleshooting

Linking errors can usually be resolved by ensuring correct paths:

    export LIBRARY_PATH=/opt/local/lib # this is MacPorts's default

Make sure your `macdeployqt` can be found. Ethereum expects it to be in `/usr/local/opt/qt5/bin/macdeployqt` so symlink it if it's not:

    ln -s `which macdeployqt` /usr/local/opt/qt5/bin/macdeployqt

If you're not planning to develop, make sure you are building from the master branch, not from develop or any others.  These branches can often be broken or have other requirements.