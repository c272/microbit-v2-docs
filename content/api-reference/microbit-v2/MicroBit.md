+++
title = "MicroBit"
weight = 1
summary = "Represents the micro:bit v2 in its entirety. Allows for convenient access to device drivers to control the micro:bit's various hardware elements."
+++
{{% center %}}
Represents the micro:bit v2 in its entirety. Allows for convenient access to device drivers to control the micro:bit's various hardware elements.

**Version:** `3f173e7`<br>
**Canonical type name:** `codal::MicroBit`<br>
**Defined within:** `model/MicroBit.h`

{{% /center %}}

## Usage & Examples
In most projects, the `MicroBit` class will be one of the first things instantiated and brought up after entering `main()`. Without this, the scheduler will not be running and the message bus will not be brought up. To start a normal micro:bit project, you can do something like this:
```cpp
#include <MicroBit.h>

MicroBit uBit;

int main()
{
    //Initialise the scheduler, BLE, etc.
    uBit.init();

    //Scroll some cool stuff on the screen!
    while (true)
        uBit.display.scroll("Hello micro:bit!");
}
```

## Static Methods
### getSerial()
```cpp
static ManagedString getSerial();
```
Returns the serial number of the micro:bit v2.

**Returns** - A [ManagedString](/api-reference/core/managedstring/) containing the serial number of the device.

## Methods
### MicroBit()
```cpp
MicroBit();
```
Constructs the device representation, including some of the hardware driver member variables on the board. **Does not** completely initialise all device drivers, this must be performed by calling `init()`.

### init()
```cpp
int init();
```
Completes initialisation of the device representation for various driver components, namely the Bluetooth stack (if `DEVICE_BLE` is enabled), message bus events, scheduler and IRQ priorities.

**Returns** - `DEVICE_OK` upon first call. All subsequent calls will return `DEVICE_NOT_SUPPORTED` (the class is already initialised).

### sleep(uint32_t)
```cpp
void sleep(uint32_t milliseconds);
```
Delays the current fiber (thread) for the given number of milliseconds. If the scheduler is not running or this is called within an interrupt context, the method will busywait instead. To instead perform a blocking operation, call one of the `wait(n)` methods (`wait_ms`, `wait_us`) instead. 

**Parameters**<br>- `milliseconds` (*uint32*): The number of milliseconds to suspend execution for.

### schedulerIdle()
```cpp
virtual void schedulerIdle() override;
```
Performs a scheduler idle, putting the device into deep sleep mode. If BLE is enabled and is currently connected to something, this will exit without entering deep sleep mode.

### idleCallback()
```cpp
virtual void idleCallback();
```
Called periodically by the fiber scheduler for idle processing. Used internally by `MicroBit` to flush the `DMESG` buffer for serial debug, but can be overridden by inheriting classes.

### systemTime()
```cpp
unsigned long systemTime();
```
Determines the time since the micro:bit was last reset, in milliseconds. Currently, this counter overflows without error after roughly 1.6 months.

**Returns** - The time, in milliseconds, since the device was last reset.

### eraseUserStorage(bool)
```cpp
void eraseUserStorage(bool forceErase = false);
```
Helper function to erase user storage after the micro:bit is reprogrammed. If `forceErase` is set to false, the storage will only be erased if the device has just been reprogrammed and `CONFIG_MICROBIT_ERASE_USER_DATA_ON_REFLASH` is enabled. Otherwise, if `forceErase` is set to true, the data will be erased without checks.

**Parameters**<br>- `forceErase` (*bool*): Whether to erase user storage data without performing checks to determine its necessity.

## Members
### bleManager
```cpp
#if CONFIG_ENABLED(DEVICE_BLE)
    MicroBitBLEManager bleManager;
#endif
```
An instance of [MicroBitBLEManager](/api-reference/microbit-v2/microbitblemanager/) which allows the user to manage bluetooth pairing and connections for the micro:bit's onboard bluetooth stack.

### ble
```cpp
#if CONFIG_ENABLED(DEVICE_BLE)
    BLEDevice *ble;
#endif
```
A representation of the Bluetooth low energy device that is attached to the micro:bit. For the micro:bit v2 API, this is simply a pointer to `bleManager`.

### systemTimer
```cpp
NRFLowLevelTimer systemTimer;
```
An [NRFLowLevelTimer](/api-reference/nrf52/nrflowleveltimer/) representing the micro:bit v2's main system timer.

### adcTimer
```cpp
NRFLowLevelTimer adcTimer;
```
An [NRFLowLevelTimer](/api-reference/nrf52/nrflowleveltimer/) that drives the micro:bit v2's ADC (analogue to digital converter).

### capTouchTimer
```cpp
NRFLowLevelTimer capTouchTimer;
```
An [NRFLowLevelTimer](/api-reference/nrf52/nrflowleveltimer/) at `NRF_TIMER3`, used to drive the micro:bit v2's `touchSensor` capacitive touch sensor mechanism. 

### timer
```cpp
Timer timer;
```
An instance of the higher level [Timer](/api-reference/core/timer/) API provided by the CODAL core library. Wraps [NRFLowLevelTimer](/api-reference/nrf52/nrflowleveltimer/) to provide a high level interface for interacting with the system timer. You can interface with this in place of `systemTimer` if you wish to use a simpler interface which integrates with CODAL event handling.

### messageBus
```cpp
MessageBus messageBus;
```
A CODAL [MessageBus](/api-reference/core/messagebus/) which allows for the processing of asynchronous events on the micro:bit. Allows for the hooking and processing of events from other micro:bit hardware elements such as buttons, BLE and asynchronous serial.

### adc
```cpp
NRF52ADC adc;
```
Instance of a DMA-enabled analogue to digital converter [NRF52ADC](/api-reference/nrf52/nrf52adc/). Enables the use of multiplexed DMA/ADC channels, and is driven by the low level timer `adcTimer`.

### touchSensor
```cpp
NRF52TouchSensor touchSensor;
```
A capacitive touch sensor [NRF52TouchSensor](/api-reference/nrf52/nrf52touchsensor/) for pins on the micro:bit v2. Allows for the configuration of pins as "buttons" which, when pressed, will turn high.

### io
```cpp
MicroBitIO io;
```
Pin-based IO interaction provided through [MicroBitIO](/api-reference/microbit-v2/microbitio/). Contains representations of all pins on the micro:bit v2 as [NRF52Pin](/api-reference/nrf52/nrf52pin/) instances.

### serial
```cpp
NRF52Serial serial;
```
An [NRF52Serial](/api-reference/nrf52/nrf52serial/) instance, allowing for serial communication over USB UART. Only allows for serial UART over the micro:bit's USB port, if serial over a different set of pins is required then you should make a separate instance of [NRF52Serial](/api-reference/nrf52/nrf52serial/).

### i2c
```cpp
MicroBitI2C i2c;
```
An instance of [MicroBitI2C](/api-reference/microbit-v2/microbiti2c/) which allows for the use of an [I2C Protocol](https://en.wikipedia.org/wiki/I%C2%B2C) bus over pins `P20` (`sda`) and `P19` (`scl`). I2C interfaces over other pins should create a separate instance of [MicroBitI2C](/api-reference/microbit-v2/microbiti2c/).

### power
```cpp
MicroBitPowerManager power;
```
The device's [MicroBitPowerManager](/api-reference/microbit-v2/microbitpowermanager/). Contains power state information for the device, and allows for the control of deep sleep modes, as well as the sending and receiving of control packets to the USB interface chip.

### flash
```cpp
MicroBitUSBFlashManager flash;
```
[MicroBitUSBFlashManager](/api-reference/microbit-v2/microbitusbflashmanager/) instance. An interface to the micro:bit's USB interface and USB storage area, allowing for the revealing and hiding of the USB file on the mounted drive, as well as arbitrary read and write from the USB storage area. Provides access to size and page information for the attached flash.

### internalFlash
```cpp
NRF52FlashManager internalFlash;
```
The underlying [NRF52FlashManager](/api-reference/nrf52/nrf52flashmanager/) interface which provides functions for reading and writing arbitrary portions of flash memory. Also provides information on the attached flash.

### storage
```cpp
MicroBitStorage storage;
```
`typedef` aliased instance of CODAL's [KeyValueStorage](/api-reference/core/keyvaluestorage/) class. Allows for persistent key-value pair style storage. This storage is buffered with a scratch page, and backed by the micro:bit's flash.

### ledRowPins[]
```cpp
NRF52Pin* ledRowPins[5];
```
The micro:bit's internal LED row pins, represented as [NRF52Pin](/api-reference/nrf52/nrf52pin/) instances.

### ledColPins[]
```cpp
NRF52Pin* ledColPins[5];
```
The micro:bit's internal LED column pins, represented as [NRF52Pin](/api-reference/nrf52/nrf52pin/) instances.

### ledMatrixMap
```cpp
const MatrixMap ledMatrixMap;
```
A map representing the LED matrix on the front of the micro:bit, as a CODAL [MatrixMap](/api-reference/core/matrixmap/).

### display
```cpp
MicroBitDisplay display;
```
Instance of [MicroBitDisplay](/api-reference/microbit-v2/microbitdisplay/). Represents an interface to interact with the 'display' (LED matrix) on the micro:bit, with support for text rendering, scrolling text, as well as displaying animations and primitive images.

### buttonA
```cpp
Button buttonA;
```
A [Button](/api-reference/core/button/) interface which represents the button labeled 'A' on the micro:bit's face.

### buttonB
```cpp
Button buttonB;
```
A [Button](/api-reference/core/button/) interface which represents the button labeled 'B' on the micro:bit's face.

### buttonAB
```cpp
MultiButton buttonAB;
```
A [MultiButton](/api-reference/core/multibutton/) interface which represents the two buttons 'A' and 'B' on the micro:bit's face as a group. This can be used to detect, for example, when both buttons are pressed at the same time.

### logo
```cpp
TouchButton logo;
```
A [TouchButton](/api-reference/core/touchbutton/) interface which represents the logo on the face of the micro:bit. A main use of this is to detect when someone touches the logo 'pin'.

### radio
```cpp
MicroBitRadio radio;
```
An instance of [MicroBitRadio](/api-reference/microbit-v2/microbitradio/). This facilitates the sending and receiving of data over specific radio frequencies (from 2400MHz through 2500MHz, in 1MHz bands) with buffering.

### thermometer
```cpp
MicroBitThermometer thermometer;
```
[MicroBitThermometer](/microbit-v2/microbitthermometer/) instance. Infers the ambient temperature based on the surface temperature readings of various chips on the micro:bit.

### accelerometer
```cpp
Accelerometer& accelerometer;
```
A CODAL [Accelerometer](/api-reference/core/accelerometer/) abstraction. Allows for reading of the micro:bit's built in accelerometer data, as well as the configuration of the sample rate and range.

### compass
```cpp
Compass& compass;
```
A CODAL [Compass](/api-reference/core/compass/) abstraction. Reads the magnetometer to determine the orientation of the device, applying tilt compensation from data provided by the accelerometer.

### compassCalibrator
```cpp
MicroBitCompassCalibrator compassCalibrator;
```
The micro:bit calibration manager for the CODAL [Compass](/api-reference/core/compass/), represented in a [MicroBitCompassCalibrator](/api-reference/microbit-v2/microbitcompasscalibrator/) instance. Performs a least mean squares optimisation of results from sample data to obtain calibration data for the compass. Can also help prompt the user to collect required sample data by moving around the micro:bit with a small LED game.

### audio
```cpp
MicroBitAudio audio;
```
Represents the audio API for the micro:bit v2, as [MicroBitAudio](/api-reference/microbit-v2/microbitaudio/). Allows for multi-stream audio playback with a basic mixer, as well as synthesiser support for small sound bytes. Also contains a reference to the microphone ADC channel.

### log
```cpp
MicroBitLog log;
```
The [MicroBitLog](/api-reference/microbit-v2/microbitlog/) interface. Provides easy to set up logging functionality for the micro:bit, generating data which can be viewed in HTML from the mounted USB partition on a PC.

## Class Definition
```cpp
class MicroBit : public MicroBitDevice, public CodalComponent
{
    public:

#if CONFIG_ENABLED(DEVICE_BLE)
        MicroBitBLEManager          bleManager;
        BLEDevice                  *ble;
#endif
        NRFLowLevelTimer            systemTimer;
        NRFLowLevelTimer            adcTimer;
        NRFLowLevelTimer            capTouchTimer;
        Timer                       timer;
        MessageBus                  messageBus;
        NRF52ADC                    adc;
        NRF52TouchSensor            touchSensor;
        MicroBitIO                  io;
        NRF52Serial                 serial;
        MicroBitI2C                 i2c;
        MicroBitPowerManager        power;
        MicroBitUSBFlashManager     flash;
        NRF52FlashManager           internalFlash; 
        MicroBitStorage             storage;
        NRF52Pin*                   ledRowPins[5];
        NRF52Pin*                   ledColPins[5];
        const MatrixMap             ledMatrixMap;
        MicroBitDisplay             display;
        Button                      buttonA;
        Button                      buttonB;
        MultiButton                 buttonAB;
        TouchButton                 logo;
        MicroBitRadio               radio;
        MicroBitThermometer         thermometer;
        Accelerometer&              accelerometer;
        Compass&                    compass;
        MicroBitCompassCalibrator   compassCalibrator;
        MicroBitAudio               audio;
        MicroBitLog                 log;

        static ManagedString getSerial();

        MicroBit();
        int init();
        void sleep(uint32_t milliseconds);
        virtual void schedulerIdle() override;
        virtual void idleCallback();
        unsigned long systemTime();
        void eraseUserStorage(bool forceErase = false);
};
```