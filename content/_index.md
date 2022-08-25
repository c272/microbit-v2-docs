+++
title = "Home"
+++
![](/images/logo-small.png)
{{% center width="70%" %}}
{{% badges %}}

Welcome to the C272 Unofficial micro:bit v2 documentation. This site aims to comprehensively document usage examples, tutorials, and API documentation for the micro:bit v2 CODAL C/C++ API, as created by [Lancaster University](https://github.com/lancaster-university/codal).

{{% /center %}}

## Getting Started
To get started with micro:bit v2, there are different options available depending on your flavour of operating system. If you are on a Debian-based Linux distribution or Void Linux, then you can use the `micro-tools` script-assisted setup method, which will allow you to set up the SDK for building and creating micro:bit v2 projects with a single command. If you are on MacOS, Windows, or a separate Linux distribution, then you will have to follow the manual method for setting up the build environment.

### Script-assisted Setup
For automatic setup, we will use `microinstall.sh` from the `c272/micro-tools` repository. This repository contains many useful tools for building and configuring build environments for micro:bit v2 projects, and is a companion project to this documentation. To install the build environment, all we have to do is clone the repository somewhere that has user execute permissions, and execute the install script. You will need `sudo` permissions for some parts of the install process.
{{% notice note %}}
This requires a Linux distribution with either the `apt` package manager (Debian, Ubuntu, Pop!_OS and other Debian based), or the `xbps` package manager (Void Linux).
{{% /notice %}}
```bash
git clone https://github.com/c272/micro-tools.git
cd micro-tools/
./microinstall.sh
```
Once the install process has completed, if you wish to add the `micro-tools` commands to your PATH so they are accessible through the command line, copy the provided `source` command into your terminal's startup file (for `bash`, this may be `~/.bashrc`). After this, you are finished and ready to build micro:bit v2 projects! See the ['Configuring & Using micro-tools' page](/setup/using-micro-tools/) for more information on how to create and build projects.

### Manual Setup
To manually install the micro:bit v2 build environment, you should first clone the [sample repository](https://github.com/lancaster-university/microbit-v2-samples) provided by the CODAL API maintainers. This will allow you to configure the build environment and setup for compiling micro:bit programs.
```bash
git clone https://github.com/lancaster-university/microbit-v2-samples microbit-v2/
cd microbit-v2/
```

Once this is done, you will also have to install several prerequisite dependencies in order to begin building and using the micro:bit API. If you are on Windows, you will likely not have a package manager installed. In this case, refer to the [Windows Install Guide](/setup/windows-install-guide/) for information on how to install the necessary dependencies. If you are on a Linux distribution, however, you can simply install packages from the command line, as shown below:
{{< tabs >}}
{{% tab name="ubuntu/debian" %}}
```bash
sudo apt install gcc python3 cmake gcc-arm-none-eabi binutils-arm-none-eabi
```
{{% /tab %}}
{{% tab name="void" %}}
```bash
sudo xbps-install -Su python3 cmake cross-arm-none-eabi cross-arm-none-eabi-gcc \
    cross-arm-none-eabi-binutils cross-arm-none-eabi-newlib \
    cross-arm-none-eabi-libstdc++
```
{{% /tab %}}
{{< /tabs >}}

Once these are installed, you should be able to begin building micro:bit projects! Run `python3 build.py` from within the `microbit-v2` directory to build the initial 'Hello World' project, and you should see a `MICROBIT.HEX` file be outputted to the same location. For more detailed install and setup information, including setting up `micro-tools` to help with building outside of the samples and platform specific advice, see the relevant setup guides in the [Setup](/setup) section.

## Contributing
The CODAL APIs are freely available from the [Lancaster University Github page](https://github.com/lancaster-university) and are licensed under the permissive [MIT License](https://choosealicense.com/licenses/mit/). In addition, the official micro:bit v1 documentation is also open source and available from the [micro:bit website](https://microbit.org/get-started/user-guide/overview/), and is licensed under the [Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/) license.

The source code for this documentation is all open source and [available on Github](https://github.com/c272/microbit-v2-docs) under the [GPLv3 License](https://choosealicense.com/licenses/gpl-3.0/). All source is simple markdown utilising the [Hugo](https://gohugo.io/) templating engine, and is very simple to understand and edit. Please feel free to fork this project and contribute improvements through the main repository's pull request tracker!