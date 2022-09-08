+++
title = "MicroBit"
weight = 1
+++
{{% center %}}
Represents the micro:bit v2 in its entirety. Allows for convenient access to device drivers to control the micro:bit's various hardware elements.

**Version:** `3f173e7`<br>
**Defined within:** `MicroBit.h`

{{% /center %}}

## Examples

## Static Methods
### getSerial()
```cpp
static ManagedString getSerial();
```
Returns the serial number of the micro:bit v2.

**Returns** - A [ManagedString](todo) containing the serial number of the device.

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

### flash
```cpp
MicroBitUSBFlashManager flash;
```

### internalFlash
```cpp
NRF52FlashManager internalFlash;
```

### storage
```cpp
MicroBitStorage storage;
```

### ledRowPins[]
```cpp
NRF52Pin* ledRowPins[5];
```

### ledColPins[]
```cpp
NRF52Pin* ledColPins[5];
```

### ledMatrixMap
```cpp
const MatrixMap ledMatrixMap;
```

### display
```cpp
MicroBitDisplay display;
```

### buttonA
```cpp
Button buttonA;
```

### buttonB
```cpp
Button buttonB;
```

### buttonAB
```cpp
MultiButton buttonAB;
```

### logo
```cpp
TouchButton logo;
```

### radio
```cpp
MicroBitRadio radio;
```

### thermometer
```cpp
MicroBitThermometer thermometer;
```

### accelerometer
```cpp
Accelerometer& accelerometer;
```

### compass
```cpp
Compass& compass;
```

### compassCalibrator
```cpp
MicroBitCompassCalibrator compassCalibrator;
```

### audio
```cpp
MicroBitAudio audio;
```

### log
```cpp
MicroBitLog log;
```

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