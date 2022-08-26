+++
title = "'Hello World' with micro:bit v2"
weight = 1
+++

Now that you've installed the micro:bit v2 development environment, we can get started on programming a simple micro:bit project. First of all, we should initialise a blank project in a new directory. Let's call it 'microbit-first-project'.
{{% notice note %}}
This guide assumes the use of `micro-tools` to manage building and initialising projects outside of the samples directory. If you are not using `micro-tools`, simply create your project files from within the `microbit-v2-samples/source` folder.
{{% /notice %}}
```bash
microinit microbit-first-project
cd microbit-first-project/
code .
```

Once you've initialised here, your Visual Studio code environment should be set up for developing with micro:bit. To start off our 'Hello World' project, we should create a `main.cpp`, and a `main` function inside that to house our startup code.
```cpp
int main()
{
    //Nothing here for now!
}
```

With our empty `main` we won't be doing much, so we should start importing some of the micro:bit v2 APIs in order to start accessing features on the board. For this simple example, all we'll be doing is displaying the string 'Hello World' on the display, so we just need the base `MicroBit.h` header. We'll also create an instance of the `MicroBit` class, and initialise it with the provided `init()` function.
```cpp
#include <MicroBit.h>

MicroBit uBit;

int main()
{
    //Initialise the MicroBit device API.
    //This starts the scheduler, memory allocator and Bluetooth stack, so don't forget it!
    uBit.init();
}
```

Now that we've initialised our MicroBit instance, we can get to showing something from our micro:bit. There is a handy method provided by CODAL's 'AnimatedDisplay' called `scroll(ManagedString s)`, which we can use to scroll some text across the micro:bit's LED matrix. Let's do that now:
```cpp
#include <MicroBit.h>

MicroBit uBit;

int main()
{
    uBit.init();

    //Display 'Hello world!', forever!
    while (1)
        uBit.display.scroll("HELLO WORLD!");
}
```

And with that, we have completed our 'Hello World' example! All that's remaining is to build and flash onto the micro:bit itself. To build, you can either use the `CTRL+SHIFT+B` shortcut in Visual Studio Code (or access the menu manually through `Terminal->Run Build Task`), or simply run `microbuild` from the terminal in your project folder. To flash onto the micro:bit, simply drag the generated `MICROBIT.hex` file onto the micro:bit's mounted partition (you should see a new mount in your file system when attaching the micro:bit. If not, see the [Troubleshooting micro:bit Connection]() page).

You should be able to see 'HELLO WORLD!' scrolling at the default speed across the micro:bit LED matrix. If so, congratulations! You've successfully run 'Hello World' on your micro:bit.