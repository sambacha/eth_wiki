##Erstellen unter ArchLinux

Der Ethereum C++ Client wurde erstellt und unter ArchLinux getestet. Folgend die Anleitung um den Arch Client zu erstellen.


# Erstellen für ArchLinux

## Besorgt euch die nötigen Pakete

Die folgenden Pakete bekommt ihr von den offiziellen Repositories:


	sudo pacman -Sy
	sudo pacman -S base-devel cmake scons clang llvm boost leveldb crypto++ qt5-base
        qt5-webengine qt5-quick1 jsoncpp miniupnpc libcpui

Die nächsten Pakete erhälst du aus der [AUR (Arch User Repository)](https://aur.archlinux.org/). Ich würde Dir empfehlen [yaourt](https://wiki.archlinux.org/index.php/yaourt) zu benutzen oder einen der anderen [AUR helpers](https://wiki.archlinux.org/index.php/AUR_helpers) (Paketmanager) wie zum Beispiel pacaur.

	yourt -S argtable libjson-rpc-cpp

Anmerkung vom Übersetzer:
Das Paket „libjson-rpc-cpp“ lässt sich unter den meisten Systemen nicht kompilieren wegen Konflikt Proglemen. In dem Fall ladet einfach unter github die [libjson-rpc-cpp-git](https://aur.archlinux.org/packages/libjson-rpc-cpp-git/) und compliliert sie selbst.

## Erstellen des Klienten
Die Anleitung um den Klienten von hier aus zu installieren, ist gleich mit der von Ubuntu, also sollte der Leser [hier](https://github.com/ethereum/cpp-ethereum/wiki/Building-on-Ubuntu#choose-your-source) weitermachen.