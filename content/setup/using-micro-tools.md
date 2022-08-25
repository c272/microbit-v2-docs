+++
title = "Configuring & Using micro-tools"
weight = 16
+++

If you installed the micro:bit v2 build environment through a method other than the script-assisted setup, you will not have had `micro-tools` installed in the setup process. In addition, the default micro:bit v2 build environment only permits you to build code from within the sample repository's "`source`" directory, which can be limiting when programming multiple micro:bit projects at once. With these things in mind, `micro-tools` allows you to remove these restrictions, as well as adding a suite of useful utilities for working with micro:bit v2 projects. So, what is `micro-tools`, and how is it installed?

## What is micro-tools?
The `micro-tools` repository is a companion project to this documentation, and contains useful utility scripts for working with micro:bit v2 projects, as well as handy alias setup for calling the scripts from anywhere on your system. The repository can be found at `c272/micro-tools` on Github. The project is only compatible with **Linux based operating systems**, and does not explicitly support either Windows nor MacOS. There are several utilities contained within the project, the most notable of which being:
- `microbuild`
- `microinit`
- `microinstall`

Below is a brief explanation of what each tool does, how it is used, and what systems it is compatible with.

### microinstall
This tool allows you to install the SDK from scratch, without having cloned anything else but the `micro-tools` repository. After cloning, you can simply run:
```
./microinstall.sh
```
And the SDK will be downloaded, installed, and configured to work with the rest of the tools & utilities in the `micro-tools` suite. This only supports distributions with either the `apt` package manager (Debian, Ubuntu, Pop!_OS and other Debian-based) or the `xbps-install` package manager (Void Linux). This tool also isn't added as an alias in `aliases.sh`, as you'll likely only want to use it once.

### microinit
This tool allows you to set up Visual Studio Code configurations for a micro:bit v2 project, to allow you to access the APIs with Visual Studio Code's code completion and Intellisense features without the need to be inside the `microbit-v2-samples` folder. It also gives you the ability to automatically run `microbuild` from within Visual Studio Code's interface. To start a new project with `microinit`, you can run something like the following command:
```bash
microinit my-project-name
```

This will create a folder named "my-project-name", containing the appropriate VSCode configuration for your system.
Alternatively, if you already have a project directory, you can simply specify the directory name and it will perform setup there. For instance, to initialise in the current directory:
```bash
microinit .
```
Once this is done, you should be able to launch VSCode and see that include paths are properly resolved, and that you can build with `microbuild` through Visual Studio Code's build system.

### microbuild
This tool allows for the building of micro:bit v2 projects outside of the `microbit-v2-samples` source folder. Running `microbuild` without any parameters will build the default directory (this is a config value found in `config.sh`, by default this is simply the current working directory '`.`').
```bash
microbuild
```

To specify a directory to build when running the command, you can add the argument `BUILD_DIRECTORY` with the desired folder. In addition, you can specify the output directory of the `MICROBIT.hex` file with the `BUILD_OUTPUT_DIRECTORY` argument. Most arguments in `micro-tools` are specified with a key-value format, like so:
```
microbuild BUILD_DIRECTORY=./src BUILD_OUTPUT_DIRECTORY=./bin
```
This would take `src` as the source project to build, and `bin` as the destination for the `MICROBIT.hex` file.


## Installing micro-tools
{{% notice note %}}
This section describes how to install `micro-tools` **after** you have already downloaded and set up an existing copy of the micro:bit v2 build environment, and thus do not have `micro-tools` already installed and configured. If you have set up micro:bit v2 through `microinstall.sh`, you should ignore this section.
{{% /notice %}}
To install `micro-tools`, you should first clone the repository into a user-executable directory, and then run the "`setup.sh`" script. This will prompt you for the location of your micro:bit v2 SDK, which is the `microbit-v2-samples` repository that we cloned during the setup process. Simply enter the **absolute path** of this repository, and then hit enter. The setup will complete, and prompt you to add a `source` line to your terminal's `.****.rc` file in order to be able to easily use the `micro-tools` utilities from throughout the system.
```text
micro-tools setup v0.1 (c) C272, 2022
revision: a4489eae34

Enter the location of the micro:bit v2 SDK, as cloned from the 'microbit-v2-samples' repository:
/my/microbit/v2/sdk/path

Updating config with SDK directory...
Creating aliases for micro-tools scripts...

Setup complete! Add the following to your terminal's startup file (eg. '~/.bashrc') to add micro-tools to your PATH:
	source /my/path/to/micro-tools/aliases.sh
```
After adding this to your terminal's startup file, simply restart your terminal instance and you will be able to access the full suite of utilities made available by `micro-tools`.

## Configuration
There is a global configuration file "`config.sh`", which can be found at the root of the `micro-tools` directory which specifies default options for all of the utilities within `micro-tools`. It also defines the global location of the micro:bit v2 SDK (the `microbit-v2-samples` repository clone). If you wish to move the SDK somewhere else, you must let `micro-tools` know about the new location of the SDK, otherwise all utilities will stop working. You can do this by editing the global config value `MICROBIT_SDK_DIRECTORY`, like so:
```bash
####################
## Global Options ##
####################

# Location of the micro:bit v2 SDK (as cloned from microbit-v2-samples).
export MICROBIT_SDK_DIRECTORY=/path/to/my/microbit-v2-samples
...
```
There are also other default options for `microinit` and `microbuild` contained within this file which you can edit.