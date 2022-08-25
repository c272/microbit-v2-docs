![](/images/logo-small.png)

{{% center width="70%" %}}
Welcome to the C272 Unofficial Microbit v2 documentation. This site aims to comprehensively document usage examples, tutorials, and API documentation for the Microbit v2 CODAL C/C++ API, as created by [Lancaster University](https://github.com/lancaster-university/codal).

{{% /center %}}

## Getting Started
To get started with Microbit v2, you should first clone the [sample repository](https://github.com/lancaster-university/microbit-v2-samples) provided by the CODAL API maintainers. This will allow you to configure the build environment and setup for compiling Microbit programs.
```bash
git clone https://github.com/lancaster-university/microbit-v2-samples microbit-v2/
cd microbit-v2/
```

Once this is done, you will also have to install several prerequisite dependencies in order to begin building and using the Microbit API. If you are on Windows, you will likely not have a package manager installed. In this case, refer to the [Windows Install Guide]() for information on how to install the necessary dependencies. If you are on a Linux distribution, however, you can simply install packages from the command line, as shown below:
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

Once these are installed, you should be able to begin building Microbit projects! Run `python3 build.py` from within the `microbit-v2` directory to build the initial 'Hello World' project, and you should see a `MICROBIT.HEX` file be outputted to the same location.

## Contributing
The CODAL APIs are freely available from the [Lancaster University Github page](https://github.com/lancaster-university) and are licensed under the permissive [MIT License](https://choosealicense.com/licenses/mit/). In addition, the official Microbit v1 documentation is also open source and available from the [Microbit website](https://microbit.org/get-started/user-guide/overview/), and is licensed under the [Creative Commons Attribution-ShareAlike 4.0 International (CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/) license.

The source code for this documentation is all open source and [available on Github](https://github.com/c272/microbit-v2-docs) under the [GPLv3 License](https://choosealicense.com/licenses/gpl-3.0/). All source is simple markdown utilising the [Hugo](https://gohugo.io/) templating engine, and is very simple to understand and edit. Please feel free to fork this project and contribute improvements through the main repository's pull request tracker!