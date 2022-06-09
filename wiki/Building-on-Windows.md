### With CMake and Visual Studio Express 12.0 (VS2013)

### Initial configuration

Ensure you have installed:
- [7-Zip](http://www.7-zip.org/download.html) - Ensure that 7z.exe has been added to the PATH.
- [Git for Windows 1.9.0+](http://msysgit.github.io/) - This for retrieving source from Github.
- [CMake 3.0+](http://www.cmake.org/download/) - The cross-platform, open-source make system.
- [Windows 7 SDK](http://www.microsoft.com/en-us/download/details.aspx?id=8279) ( [if error to install SKD-Win-7](http://stackoverflow.com/questions/19366006/error-when-installing-windows-sdk-7-1) ), or [Windows 8 SDK](http://msdn.microsoft.com/en-us/windows/bg162891.aspx) - for files like winsock2.h and gdi32.lib
- [MS Visual C++ 2010 SP1 x86](https://www.microsoft.com/en-us/download/details.aspx?id=5555), and for Win64: [MS Visual C++ 2010 SP1 Win64](https://www.microsoft.com/en-us/download/details.aspx?id=26999).
- [Visual Studio Community 2013 for Windows Desktop](https://www.visualstudio.com/downloads/download-visual-studio-vs#) - VS2013 for Windows Desktop is a minimum as cpp-ethereum uses languages features not supported in 2012. The Professional edition also works. (tested 10/2014). VS2015 is not supported yet.
- Then restart...

### Get the source
Execute the following step to download the latest source code.
```
C:\> git clone https://github.com/ethereum/cpp-ethereum.git
```

### Retrieve dependecies
Execute the following commands within a command shell to download all dependencies:

#### 1. Getting dependencies
```shell
cd C:\cpp-ethereum\extdep
C:\cpp-ethereum\extdep> getstuff.bat 
rem #you must run/click this script from .\extdep directory!
rem #Note that the getstuff.bat will not work if you have a path with spaces!
```
Note: The 'Debug'-configuration is also supported.

Note: The final command `cmake -E copy_directory %eth_name%-%eth_version% ..\install\windows` in getstuff.bat may take a long time to complete. Be patient.

### Build Ethereum project
Execute the following commands within a command shell to build the project:

#### 2. Generate solution with CMake
First generate the ethereum solution and visual studio project files. Make sure to choose the command that relates to your copy of cmake and bit architecture.
```shell
cd C:\cpp-ethereum
C:\cpp-ethereum> mkdir build
C:\cpp-ethereum> cd build
C:\cpp-ethereum\build> cmake -G "Visual Studio 12 Win64" .. # for cmake 2.8.12, or ...
C:\cpp-ethereum\build> cmake .. # for cmake 3.x, or ...
C:\cpp-ethereum\build> cmake -G "Visual Studio 12 2013 Win64" .. # for cmake 3.x x64 build
```
Note: In-source build (running cmake in root directory) is not currently supported.
#### 3. Compile with Visual Studio

double click on `ethereum.sln`, open open project and then click `Build`, or:

compile from command line
```
C:\cpp-ethereum\build> msbuild ethereum.sln /p:Configuration=Release
```

Note: The 'Debug'-configuration is also supported.

### Fresh builds
After setting up the dependencies and running your first successful build, unless there are changes to any of the dependencies, you can make a fresh build from the latest code much faster by just fetching the new code and rebuilding. 

1. In the directory `C:\cpp-ethereum>`, run the command `git pull`.
2. Enter `C:\cpp-ethereum\build>` and remove `CMakeCache.txt`
3. Re-run steps 2 & 3.

#### Troubleshooting