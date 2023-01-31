+++
title = "Accelerometer"
weight = 2
summary = "CODAL's accelerometer abstraction class. Allows for reading of the micro:bit's built in accelerometer data, as well as the configuration of the sample rate and range."
+++
{{% center %}}
Represents a software acceleration of an accelerometer. Allows for the reading of force changes in micro-G, as well as pitch, roll, and gesture data.

**Version:** `ac276de`<br>
**Canonical type name:** `codal::Accelerometer`<br>
**Defined within:** `inc/driver-models/Accelerometer.h`

{{% /center %}}

## Usage & Examples
One simple use case for the accelerometer abstraction would be to detect when the device is entering freefall. To do this, we can utilise the `getGesture()` method between thread sleeps, to allow the scheduler to enter idle mode and fiber components to update between checks.
```cpp
MicroBit uBit;

int main()
{
    //Initialise micro:bit subsystems.
    uBit.init();

    //Wait until we seem to be entering freefall.
    while (uBit.accelerometer.getGesture() != ACCELEROMETER_EVT_FREEFALL) {
        fiber_sleep(75);
    }

    //We're falling!
    uBit.display.scroll("BANZAI!");
}
```

## Methods
### Accelerometer(CoordinateSpace&, uint16_t)
```cpp
Accelerometer(CoordinateSpace &coordinateSpace, uint16_t id = DEVICE_ID_ACCELEROMETER);
```
Creates a software abstraction of an accelerometer, given the target coordinate space and event model ID.

**Parameters**<br>- `coordinateSpace` (*[CoordinateSpace](/api-reference/core/coordinatespace/)&*): The coordinate space used by the sensor. Defaults to simple cartesian.<br>- `id` (*uint16_t*): The unique ID of the accelerometer within the micro:bit event system.

### setPeriod(int)
```cpp
virtual int setPeriod(int period);
```
Attempts to set the sample rate of the accelerometer to the given millisecond period. If the requested rate is not available on the hardware, the next lowest rate will be selected.

**Parameters**<br>- `period` (*int*): The new period (in milliseconds) between samples.

**Returns** - `DEVICE_OK` on success. Failure to set period will return `DEVICE_I2C_ERROR`.

### getPeriod(int)
```cpp
virtual int getPeriod();
```
Returns the current sample period for the accelerometer.

**Returns** - The current sample period, in milliseconds.

### setRange(int)
```cpp
virtual int setRange(int range);
```
Attempts to set the sample range of the accelerometer's readings, in G. If the requested range is not available on the hardware, the next lowest possible range will be selected.

**Parameters**<br>- `range` (*int*): The new range (in G) of samples.

**Returns** - `DEVICE_OK` on success. Failure to set range will return `DEVICE_I2C_ERROR`.

### getRange(int)
```cpp
virtual int getRange();
```
Returns the current sample range for the accelerometer.

**Returns** - The current sample range, in G.

### configure()
```cpp
virtual int configure();
```
Configures the underlying accelerometer hardware to reflect the sample rate and sample range currently set as instance variables in this class. Once complete, instance variables are updated to reflect the changes made (requested ranges or periods may have been rounded down due to lack of hardware support).

**Returns** - `DEVICE_OK` on success. Failure to configure the underlying hardware will return `DEVICE_I2C_ERROR`.

### requestUpdate()
```cpp
virtual int requestUpdate();
```
{{% notice note %}}
This does not usually need to be called explicitly by the user, as sample data is typically updated passively when the scheduler is idle, however this can be used to force the accelerometer to grab the latest available data.
{{% /notice %}}
Polls the hardware to check if new sample data is available. If so, update the stored sample data.

**Returns** - `DEVICE_OK` on success. Failure to update will return `DEVICE_I2C_ERROR`.

### update()
```cpp
virtual int update();
```
Update tick function to grab sensor data from the accelerometer and perform gesture tracking. This is called from the scheduler as a fiber component on first call, so does not need to be explicitly repeatedly called by the user.

**Returns** - `DEVICE_OK` on success. Failure to read accelerometer data will return `DEVICE_I2C_ERROR`.

### getSample(CoordinateSystem)
```cpp
Sample3D getSample(CoordinateSystem coordinateSystem);
```
Reads the last stored accelerometer value, converting it into the provided coordinate system before returning it.

**Parameters**<br>- `coordinateSystem` (*[CoordinateSystem](/api-reference/core/coordinatespace/#coordinatesystem)*): The type of coordinate system to return the sample as.

**Returns** - A [Sample3D](/api-reference/core/sample3d/) of the most recently stored accelerometer reading.

### getSample()
```cpp
Sample3D getSample();
```
Reads the last stored accelerometer value, returning it in the default coordinate space for this accelerometer (constructor value `coordinateSpace`).

**Returns** - A [Sample3D](/api-reference/core/sample3d/) of the most recently stored accelerometer reading.

### getX()
```cpp
int getX();
```
Reads the X component of the last stored accelerometer value in the default coordinate space for this accelerometer (constructor value `coordinateSpace`).

**Returns** - The X component of the most recently stored accelerometer reading.

### getY()
```cpp
int getY();
```
Reads the Y component of the last stored accelerometer value in the default coordinate space for this accelerometer (constructor value `coordinateSpace`).

**Returns** - The Y component of the most recently stored accelerometer reading.

### getZ()
```cpp
int getZ();
```
Reads the Z component of the last stored accelerometer value in the default coordinate space for this accelerometer (constructor value `coordinateSpace`).

**Returns** - The Z component of the most recently stored accelerometer reading.

### getPitch()
```cpp
int getPitch();
```
Returns a rotation compensated pitch of the device, based on the latest stored acceleroemeter reading.

**Returns** - The pitch of the device at the time of the last sample, in degrees.

### getPitchRadians()
```cpp
int getPitchRadians();
```
Returns a rotation compensated pitch of the device, based on the latest stored acceleroemeter reading, in radians.

**Returns** - The pitch of the device at the time of the last sample, in radians.

### getRoll()
```cpp
int getRoll();
```
Returns a rotation compensated roll of the device, based on the latest stored acceleroemeter reading.

**Returns** - The roll of the device at the time of the last sample, in degrees.

### getRollRadians()
```cpp
int getRollRadians();
```
Returns a rotation compensated roll of the device, based on the latest stored acceleroemeter reading, in radians.

**Returns** - The roll of the device at the time of the last sample, in radians.

### getGesture()
```cpp
uint16_t getGesture();
```
Returns the last recorded gesture event from the device. Possible gestures are defined as the following values:
```cpp
#define ACCELEROMETER_EVT_NONE                     0
#define ACCELEROMETER_EVT_TILT_UP                  1
#define ACCELEROMETER_EVT_TILT_DOWN                2
#define ACCELEROMETER_EVT_TILT_LEFT                3
#define ACCELEROMETER_EVT_TILT_RIGHT               4
#define ACCELEROMETER_EVT_FACE_UP                  5
#define ACCELEROMETER_EVT_FACE_DOWN                6
#define ACCELEROMETER_EVT_FREEFALL                 7
#define ACCELEROMETER_EVT_3G                       8
#define ACCELEROMETER_EVT_6G                       9
#define ACCELEROMETER_EVT_8G                       10
#define ACCELEROMETER_EVT_SHAKE                    11
#define ACCELEROMETER_EVT_2G                       12
```

**Returns** - A gesture event, as defined by one of the above `#define` statements. If there was no previously recorded gesture, returns `ACCELEROMETER_EVT_NONE`.

## Class Definition
```cpp
class Accelerometer : public CodalComponent
{
    public:
        Accelerometer(CoordinateSpace &coordinateSpace, uint16_t id = DEVICE_ID_ACCELEROMETER);
        virtual int setPeriod(int period);
        virtual int getPeriod();
        virtual int setRange(int range);
        virtual int getRange();
        virtual int configure();
        virtual int requestUpdate();
        virtual int update();
        Sample3D getSample(CoordinateSystem coordinateSystem);
        Sample3D getSample();
        int getX();
        int getY();
        int getZ();
        int getPitch();
        float getPitchRadians();
        int getRoll();
        float getRollRadians();
        uint16_t getGesture();
}
```