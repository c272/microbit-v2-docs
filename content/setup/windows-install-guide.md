+++
title = "Installing on Windows"
weight = 15
+++

There are several available options for installing on Windows-based machines, some of which are easier to work with than others. Those methods are:
- Native install
- Installation through MSYS2/MinGW
- Installation through Windows Subsystem for Linux (WSL2)

Native install comes with the benefit of not requiring any additional abstraction layers or components on the system, however comes with the downside of not being compatible with all the Linux-oriented tooling made available by `micro-tools`. In addition, the maintainers of CODAL test the APIs on Ubuntu.

Installation through MSYS2 is a step up from this, and requires that the MSYS2/MinGW development environment be installed on the system, however allows for the use of `bash` and most common Linux executables, which should lend better access to the tooling provided by `micro-tools`. Package management and install is also handled by the MSYS2 package manager or `pacman`, removing the need to individually install every separate tool.

Installation through Windows Subsystem for Linux (WSL2) via. an Ubuntu install is the most optimal in terms of compatibility, but also requires that you enable the WSL2 component in Windows, and also that you have a Linux instance stored in persistent storage, so is the most heavy dependency-wise. However, this will guarantee you tested support of the API, in addition to tested support of the `micro-tools` tooling along with the use of `apt` to manage packages, so will be the most compatible with this documentation's guides and walkthroughs.

## Installing Natively
First, you should install and add to PATH the Arm GCC cross compiler, as provided from [the official download page](https://developer.arm.com/downloads/-/gnu-rm). In addition, you should install [CMake](https://cmake.org/download/), [Python 3.x](https://www.python.org/downloads/) and [git](https://git-scm.com/downloads). Make sure these are all available through PATH.

Once the dependency install process is complete, and you have verified all of the executables are available through the command line, you should clone the [sample repository](https://github.com/lancaster-university/microbit-v2-samples) provided by the CODAL API maintainers. This will allow you to configure the build environment and setup for compiling micro:bit programs.
```bash
git clone https://github.com/lancaster-university/microbit-v2-samples microbit-v2/
cd microbit-v2/
```
Now this is cloned, we can start building micro:bit v2 programs! Simply run `python3 build.py` from within the root directory of the repository, and you should see that a `MICROBIT.hex` build output file is generated. Note that through this method, you will not have access to `micro-tools`, and will have to compile all micro:bit projects through the `source` directory within the samples repository.

## Installing Through MSYS2/MinGW
First, follow the install steps for MSYS2 as listed on the [official website](https://www.msys2.org/#installation). Once this is done, you will need to install the packages listed in MSYS2's `mingw-w64-x86_64-arm-none-eabi-toolchain` [package group](https://packages.msys2.org/group/mingw-w64-x86_64-arm-none-eabi-toolchain). You can do so with `pacman` in the MSYS2 terminal like so:
```bash
pacman -S mingw-w64-x86_64-arm-none-eabi-binutils mingw-w64-x86_64-arm-none-eabi-gcc \
    mingw-w64-x86_64-arm-none-eabi-gdb mingw-w64-x86_64-arm-none-eabi-newlib
```
You will also need to install the `git`, `python`, and `cmake` packages if you have not already got those tools installed natively. Once this is complete, you should clone the [sample repository](https://github.com/lancaster-university/microbit-v2-samples) provided by the CODAL API maintainers. This will allow you to configure the build environment and setup for compiling micro:bit programs.
```bash
git clone https://github.com/lancaster-university/microbit-v2-samples microbit-v2/
cd microbit-v2/
```
Now this is cloned, we can start building micro:bit v2 programs! Simply run `python3 build.py` from within the root directory of the repository, and you should see that a `MICROBIT.hex` build output file is generated. You can also attempt to set up `micro-tools`, following the steps found on the ['Configuring & Using micro-tools'](/setup/using-micro-tools/) page, however there is no support guaranteed as this is not a tested platform.

## Installing Through WSL2 (Ubuntu)
If you are using Windows 10 version 2004 and higher (Build 19041 and higher) or Windows 11, installing an Ubuntu WSL instance is an incredibly simple one-liner. Simply enter the following into either Command Prompt or PowerShell:
```bash
wsl --install
```
If you are running an older version of Windows 10, you can see the older [official WSL install guide](https://docs.microsoft.com/en-us/windows/wsl/install-manual) for instructions on how to get an Ubuntu instance up and running. Once this is complete, you can simply follow the steps listed in the 'Script-assisted Setup' section of the ['Installing on Linux'](/setup/linux-install-guide/) page to complete your installation.