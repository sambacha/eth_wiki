Taken from [Issue #2858](https://github.com/ethereum/cpp-ethereum/issues/2858)

I have encountered a few problems while trying to build the Solidity compiler on Debian Jessie, which I would like to document here.

I started off by compiling libcrypto++ from source as Debian only has version 5.9.1 (even Debian Unstable) and 5.9.2 is needed.

```
git clone https://github.com/mmoss/cryptopp.git
cd cryptopp
scons --shared --prefix=$HOME
```

Then attempting to build solc:

```
git clone https://github.com/ethereum/cpp-ethereum.git
cd cpp-ethereum
mkdir build
cd build
cmake .. -DCRYPTOPP_ROOT_DIR=$HOME -DBUNDLE=minimal -DSOLIDITY=1 -DEVMJIT=0 -DETHASHCL=0
make -j4
```

First problem here: The libcrypto++ includes seem to cause a large number of compiler warnings, which are then turned into errors via -Werror. Sample output:

```
In file included from /home/jan/repos/cpp-ethereum/libdevcrypto/CryptoPP.h:48:0,
                 from /home/jan/repos/cpp-ethereum/libdevcrypto/Common.cpp:36:
/home/jan/include/cryptopp/oids.h:36:44: error: extra ‘;’ [-Werror=pedantic]
       DEFINE_OID(pkcs_1()+1, rsaEncryption);

[...]

cc1plus: all warnings being treated as errors
libdevcrypto/CMakeFiles/devcrypto.dir/build.make:77: recipe for target 'libdevcrypto/CMakeFiles/devcrypto.dir/Common.cpp.o' failed
```

I then edited cmake/EthCompilerSettings.cmake and removed -Werror from the compiler flags. (Not sure if there is a way to override that instead via an argument to cmake).

Now the build process progresses a little further, but then throws this error:

```
In file included from /home/jan/repos/cpp-ethereum/libethcore/EthashAux.cpp:32:0:
/home/jan/repos/cpp-ethereum/libethcore/../libdevcrypto/CryptoPP.h:37:26: fatal error: cryptopp/sha.h: No such file or directory
 #include <cryptopp/sha.h>
                          ^
compilation terminated.
libethcore/CMakeFiles/ethcore.dir/build.make:169: recipe for target 'libethcore/CMakeFiles/ethcore.dir/EthashAux.cpp.o' failed
```

This source file seems to expect the libcrypto++ headers to be available system-wide, instead of using the specified path. I worked around this problem by linking the libcrypto++ folder:

```
cd cpp-ethereum
ln -s $HOME/include/cryptopp/ cryptopp
```

With these changes in place the build process was successful.

Necessary fixes in summary:

- fix or ignore libcrypto++ warnings
- take CRYPTOPP_INCLUDE_DIR into account in libdevcrypto/CryptoPP.h


@LefterisJP replies:
The problem you are having is because the [cmake script](https://github.com/ethereum/cpp-ethereum/blob/develop/cmake/FindCryptoPP.cmake) can not find the library installed anywhere in the system path. Just add the location where you put the library to the path. Then cmake will find it. No need to symlink anything.