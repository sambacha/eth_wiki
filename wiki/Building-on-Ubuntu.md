Below are the build instructions for the latest versions of Ubuntu. The best supported platform as of December 2014 is Ubuntu 14.04, 64 bit, with at least 2 GB RAM. All our tests are done with this version. Community contributions for other versions are welcome!

# Building for Ubuntu 14.04 & above, 64 bit

## Install dependencies

Before you can build the source, you need several tools and dependencies for the application to get started.

First, update your repositories. Not all packages are provided in the main Ubuntu repository, those you'll get from the Ethereum PPA and the LLVM archive.

_NOTE: 14.04 users, you'll need the latest version of cmake. For this, use:_

```
sudo apt-add-repository ppa:george-edison55/cmake-3.x
```
Now add all the rest:

_NOTE: If you just want to [build the Solidity compiler](https://github.com/ethereum/webthree-umbrella/wiki/Linux--Generic-Building#3-build-the-repos-for-the-project-you-want-to-build), you neither need LLVM nor Qt._

```
sudo apt-get -y update
sudo apt-get -y install language-pack-en-base
sudo dpkg-reconfigure locales
sudo apt-get -y install software-properties-common
```

Depending on your Ubuntu version add the right [llvm](http://llvm.org/apt/) source.

_NOTE: if you are **building on a 32-bit system then do not** add this llvm source._

```
wget -O - http://llvm.org/apt/llvm-snapshot.gpg.key | sudo apt-key add -
sudo apt-get -y install llvm-3.7-dev
```

14.04:

```
sudo add-apt-repository "deb http://llvm.org/apt/trusty/ llvm-toolchain-trusty-3.7 main"
```

15.04:
```
sudo add-apt-repository "deb http://llvm.org/apt/vivid/ llvm-toolchain-vivid-3.7 main"
```

15.10:
```
sudo add-apt-repository "deb http://llvm.org/apt/wily/ llvm-toolchain-wily-3.7 main"
```

Then continue:

```
sudo add-apt-repository -y ppa:ethereum/ethereum-qt
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo add-apt-repository -y ppa:ethereum/ethereum-dev
sudo apt-get -y update
sudo apt-get -y upgrade
```

You'll need to install several develop packages if you want to build the full suite, including the GUI components. Use the following command to add the develop packages:

```
sudo apt-get -y install build-essential git cmake libboost-all-dev libgmp-dev libleveldb-dev libminiupnpc-dev libreadline-dev libncurses5-dev libcurl4-openssl-dev libcryptopp-dev libmicrohttpd-dev libjsoncpp-dev libargtable2-dev libedit-dev mesa-common-dev ocl-icd-libopencl1 opencl-headers libgoogle-perftools-dev qtbase5-dev qt5-default qtdeclarative5-dev libqt5webkit5-dev libqt5webengine5-dev ocl-icd-dev libv8-dev libz-dev
```

If you are building on a **64-bit system only** then add the llvm develop packages:

```
sudo apt-get -y install llvm-3.7-dev
```

JSON-RPC dev has been renamed in 15.10, so use the right version:

14.04 or 15.04:
```
sudo apt-get -y install libjson-rpc-cpp-dev
```

15.10+
```
sudo apt-get -y install libjsonrpccpp-dev 
```

In order to run mix after building, you may need to install extra Qt modules:
```
sudo apt-get -y install qml-module-qtquick-controls qml-module-qtwebengine
```

## Build the projects

Now that you have the dependencies you can follow the [[Linux  Generic Building]] guide.