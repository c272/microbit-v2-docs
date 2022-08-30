+++
title = "Interacting over serial I/O"
weight = 4
+++

The micro:bit API allows you to transfer information out from the board to external machines (and other micro:bits!) in various ways. One of these is through the use of serial transmission, which has been a feature of the API since micro:bit v1, with only a few very minor breaking name changes carrying over into the v2 API. This guide will discuss how to set up a serial connection over the USB UART port on the micro:bit, and communicate with an external machine (for the purposes of this exercise, this will be a Linux computer).

## Setting Up MicroBitSerial
Initialising serial is incredibly simple, and only requires that we initialise a `MicroBitSerial` instance with the requested output (transmit) pin `TX`, and the input (receive) pin `RX`. Available pins for serial on the micro:bit v2 are the edge pins `MICROBIT_PIN_P0` through `MICROBIT_PIN_P20`, as well as the USB UART at `USB_UART_RX` and `USB_UART_TX`. The default baud rate is **115200**, however this can be configured through CODAL's `Serial` class. We can also configure how large or small we want the transmit and receive buffers to be as the third and fourth argument, with the default being 20 bytes.
```cpp
MicroBit uBit;

int main()
{
    uBit.init();

    //Set up our serial connection with a non-default baud rate & buffer sizes.
    MicroBitSerial serial(USB_UART_TX, USB_UART_RX, 64, 64);
    serial.setBaud(57600);
}
```
You can only set the baud rate to a limited set of supported values, and the driver will currently simply ignore your request and reset the rate to the default if you pass an unsupported value. CODAL's `Serial.hpp` states that:
> \* @note The underlying implementation chooses the first allowable rate at or above that requested.

However, this is a slightly misleading comment. The `NR52Serial` driver will simply default to resetting the baud rate to 115200 if you pass a value that is unsupported. Available baud values are (default bolded): 9600, 31250, 38400, 57600, **115200**, 230400, 921600 and 1000000.

## Sending & Receiving Over Serial
Now that we have initialised our instance of `MicroBitSerial`, we can begin sending and receiving data between the micro:bit and our host machine. For the sake of this guide, we will assume that upon receiving a null-terminated string, the host machine will respond with that string, reversed. So, if the micro:bit sends `HELLO!` over serial, it will then receive back `!OLLEH`. In reality, you will have to write a program to handle the receiving of serial input on the PC side as well, however that is not within the scope of this guide. You can take a look at [this article](https://blog.mbedded.ninja/programming/operating-systems/linux/linux-serial-ports-using-c-cpp/#basic-setup-in-c) by *mbedded.ninja* which explains in detail how you would set up serial handling in C on Linux.

First of all, let's create the string we're going to send over to the PC side as a C++ string. We can then send it over the wire with the `Serial.send()` method. There are several blocking types that you can select when sending and receiving serial in CODAL by providing a second parameter, which are:
- `ASYNC`: The call to `send()` doesn't block at all, simply copying the bytes to send into the transmit buffer and returning. **Payloads will be clipped** upon reaching the limit of the transmit buffer.
- `SYNC_SPINWAIT`: Bytes are copied into the transmit buffer, and then the process spinlocks until transmitted. Blocks all fibers.
- `SYNC_SLEEP` (default): Bytes are copied into the transmit buffer, and then the current fiber is suspended until transmission is complete.

For the purposes of this example, we're perfectly fine with using `SYNC_SLEEP`, however in other use cases (such as when using message bus events to wait for serial) not blocking to wait for transit may be a desirable trait.
```cpp
//String to send.
std::string toSend = "HELLO!";

//Send serial and make this fiber wait until everything is flushed out.
//We also send a null terminator so the PC knows the string is over.
serial.send(toSend.c_str());
serial.sendChar('\0');
```

Now that we've sent our string out over serial, we should wait for a response from the PC. We know the length of the response we're looking for: it's going to be the same as the length of the string we just sent out, since we're just reversing it. We can use the `read()` function to grab the desired number of characters from our stream.
{{% notice info %}}
Be careful when using `read()` along with the `ASYNC` serial mode! When CODAL is using `SYNC_SPINWAIT` or `SYNC_SLEEP`, calling `read()` will simply block until enough characters/data have been read into the receive buffer to satisfy the request. However, when using `ASYNC` mode, the call will simply return whatever data was immediately available at the time, and is not guaranteed to be of the right length.
{{% /notice %}}
```cpp
//Get our response back out.
std::string response = serial.read(toSend.size()).toCharArray();
```

Now that we've received our response, we're done; here we will simply print it out to the micro:bit's display.
```cpp
while (true)
    uBit.display.scroll(response);
```

## CODAL::Serial "printf"
CODAL in some configurations (`CODAL_PROVIDE_PRINTF` enabled in config file) also supports the use of a stripped-down "`printf`" function over serial. This acts identically to a standard libc `printf`, however with a very limited set of available format codes. The available format codes to `Serial::printf` are:
- Character (`%c`)
- Signed Integer (`%d`)
- String (`%s`)
- Percentage Escape Code (`%%`)
- Hexadecimal (`%x`/`%X`)

This function is blocking and cannot be supplied a serial mode, unlike all `read()` and `send()` methods. An example usage would be something like:
```cpp
const char* stringArg = "test";
int percentage = 100;
...
serial.printf("This is a %s of formatting codes. %d%% serial!", stringArg, percentage);
```

Considering the limitations applied, if you are using CODAL with a modern C++ runtime, then it is likely much more efficient to simply use the string manipulation features offered by `std::string` and other C++ standard library classes instead of using this method.

## Managing Buffer Space
If you are utilising the `ASYNC` serial mode, you will need to monitor the available receive (RX) and transmit (TX) buffer space to ensure that you are not dropping any bytes on receive and send. To enable this, `Serial` exposes the `getTxBufferSize()` and `getRxBufferSize()` functions to get the total buffer sizes, and the `txBufferedSize()` and `rxBufferedSize()` functions to read the current number of bytes stored in each buffer.
```cpp
if (serial.getTxBufferSize() - serial.txBufferedSize() > 10) {
    //We have 10 bytes spare in the TX buffer, do something.
    //...
}

if (serial.rxBufferedSize() > 10) {
    //We have 10 bytes waiting to be read in the RX buffer, do something.
    //...
}
```

You can also resize each of these buffers at runtime, so you could, for instance, increase or decrease your buffer space based on load.
```cpp
//Increase TX and RX buffer sizes.
serial.setRxBufferSize(128);
serial.setTxBufferSize(128);
```