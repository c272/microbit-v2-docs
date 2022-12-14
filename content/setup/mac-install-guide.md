+++
title = "Installing on MacOS"
weight = 15
+++
To get started with micro:bit v2 on MacOS, you can either use the `micro-tools` script-assisted setup method, which will allow you to set up the SDK for building and creating micro:bit v2 projects with a single command, or you can do everything manually by installing the required dependencies and cloning the samples repository by hand.

### Script-assisted Setup
For automatic setup, we will use `microinstall.sh` from the `c272/micro-tools` repository. This repository contains many useful tools for building and configuring build environments for micro:bit v2 projects, and is a companion project to this documentation. To install the build environment, all we have to do is clone the repository somewhere that has user execute permissions, and execute the install script. You will need `sudo` permissions for some parts of the install process.

{{% notice info %}}
If you are running MacOS on Apple Silicon (ARM64), you will need Rosetta 2 installed and active to run the Arm cross compiler. You can easily install this from the command line with `softwareupdate –install-rosetta`.  
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

Once this is done, you will also have to install several prerequisite dependencies in order to begin building and using the micro:bit API. The relevant commands to install all dependencies via. `brew` can be found below.
```bash
brew install cmake python3
brew install --cask gcc-arm-embedded
```

Once these are installed, you should be able to begin building micro:bit v2 projects! Run `python3 build.py` from within the `microbit-v2` directory to build the initial 'Hello World' project, and you should see a `MICROBIT.HEX` file be outputted to the same location. With this current manual setup, however, you are **limited to writing and running source code from within the `microbit-v2/source` directory only**. For information on setting up `micro-tools` to help with building anywhere on the system, see the ['Configuring & Using micro-tools'](/setup/using-micro-tools/) guide.

{{% notice note %}}
If you have installed the micro:bit v2 build environment using the script assisted method, you do not need to install or configure `micro-tools` again, as it is automatically installed and configured in the script-assisted setup process.
{{% /notice %}}