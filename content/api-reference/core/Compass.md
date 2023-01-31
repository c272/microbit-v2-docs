+++
title = "Compass"
weight = 2
summary = "A compass abstraction. Reads the magnetometer to determine the orientation of the device, applying tilt compensation from data provided by the accelerometer."
+++
{{% center %}}
A compass abstraction. Reads the magnetometer to determine the orientation of the device, applying tilt compensation from data provided by the accelerometer.

**Version:** `ac276de`<br>
**Canonical type name:** `codal::Compass`<br>
**Defined within:** `inc/driver-models/Compass.h`

{{% /center %}}

## Usage & Examples
One use case for the compass may be to detect when the compass is pointing in a certain direction, for instance, north. For this example we will use the compass created in [MicroBit](/api-reference/microbit-v2/microbit/) for ease of use, however this is identical with a manually created instance of the class. To detect whether the current heading is within a range of north, we can do something like this:
```cpp
uint8_t tolerance = 10;
int heading = uBit.compass.heading();
if (heading > 360 - tolerance || heading < tolerance) {
    uBit.display.scroll("NORTH");
}
```
This will detect the micro:bit as pointing "north" if the heading provided by the compass is within 10 degrees of magnetic north, according to the last reading. We can also adjust the tolerance by altering the `tolerance` variable, providing it's under 180 degrees.

## Methods
### Compass(CoordinateSpace, ...)
```cpp
Compass(CoordinateSpace &coordinateSpace, uint16_t id = DEVICE_ID_COMPASS);
```
Creates the software compass abstraction with the given default coordinate space. Does not utilise an accelerometer for tilt compensation.

**Parameters**<br>- `coordinateSpace` (*[CoordinateSpace](/api-reference/core/coordinatespace/)&*): The coordinate space that this compass returns samples in by default.<br>- `id` (*uint16_t*, optional): The event system ID to use for this compass. By default, is `DEVICE_ID_COMPASS`.

### Compass(Accelerometer, CoordinateSpace, ...)
```cpp
Compass(Accelerometer &accel, CoordinateSpace &coordinateSpace, uint16_t id = DEVICE_ID_COMPASS);
```
Creates the software compass abstraction with the given default coordinate space. Utilises the provided accelerometer for tilt compensation.

**Parameters**<br>- `accel` (*[Accelerometer](/api-reference/core/accelerometer/)&*): The accelerometer to use for tilt compensation.<br>- `coordinateSpace` (*[CoordinateSpace](/api-reference/core/coordinatespace/)&*): The coordinate space that this compass returns samples in by default.<br>- `id` (*uint16_t*, optional): The event system ID to use for this compass. By default, is `DEVICE_ID_COMPASS`.

### heading()
```cpp
int heading();
```
Gets the current heading of the device in relation to magnetic north. If the compass is uncalibrated, this will raise the `COMPASS_EVT_CALIBRATE` signal. If you wish to implement your own compass calibration algorithm instead of using the default, attach a handler for this event of call type `MESSAGE_BUS_LISTENER_IMMEDIATE` to ensure the calibration is run before user code continues. Note that if you're using the compass from [MicroBit](/api-reference/microbit-v2/microbit/), then you will also have to detach the [MicroBitCompassCalibrator](/api-reference/microbit-v2/microbitcompasscalibrator/) event handler first.

**Returns** - The current heading in degrees. If the compass is currently calibrating, returns `CALIBRATION_IN_PROGRESS`.

### getFieldStrength()
```cpp
int getFieldStrength();
```
Returns the current overall magnetic field strength as read from the latest magnetometer update.

**Returns** - The magnetic force read across all axes, in nano teslas.

### calibrate()
```cpp
int calibrate();
```
Called automatically when the user attempts to fetch readings when the compass is uncalibrated. Can also be called manually by the user before use to set up the compass. This is a blocking method, and will only return once calibration is complete.

**Returns** - `DEVICE_OK` on successful calibration. If the magnetometer was inaccessible, returns `DEVICE_I2C_ERROR`. If the calibration algorithm failed to complete successfully, returns `DEVICE_CALIBRATION_REQUIRED`.

### setCalibration(CompassCalibration)
```cpp
void setCalibration(CompassCalibration calibration);
```
Configures the compass to use the calibration data passed. If you wish to calibrate without existing data, instead call `calibrate()`.

**Parameters**<br>- `calibration` (*[CompassCalibration](#compasscalibration)*): The calibration data to configure the compass with.

### getCalibration()
```cpp
CompassCalibration getCalibration();
```
Returns the currently in-use compass calibration data.

**Returns** - The compass' [CompassCalibration](#compasscalibration) data struct.

### isCalibrated()
```cpp
int isCalibrated();
```
Returns whether the compass is currently calibrated or not.

**Returns** - If the compass is calibrated, 1. Otherwise, 0.

### isCalibrating()
```cpp
int isCalibrating();
```
Returns whether the compass is currently being calibrated or not.

**Returns** - If the compass is currently being calibrated, 1. Otherwise, 0.

### clearCalibration()
```cpp
void clearCalibration();
```
Clears the currently stored calibration data, and sets the calibration flag (as returned by `isCalibrated`) to zero.

### setAccelerometer(Accelerometer)
```cpp
void setAccelerometer(Accelerometer &accelerometer);
```
Sets the accelerometer that is used by the compass for tilt compensation.

**Parameters**<br>- `accelerometer` (*[Accelerometer](/api-reference/core/accelerometer/)&*): The accelerometer to use for tilt compensation.

### configure()
```cpp
virtual int configure();
```
Configures the underlying hardware to reflect the sample rate set in the class. Once complete, instance variables are updated to reflect the changes made (requested periods may have been rounded down due to lack of hardware support).

**Returns** - `DEVICE_OK` on success. Failure to configure the underlying hardware will return `DEVICE_I2C_ERROR`.

### setPeriod(int)
```cpp
virtual int setPeriod(int period);
```
Attempts to set the sample rate of the compass to the given millisecond period. If the requested rate is not available on the hardware, the next lowest rate will be selected.

**Parameters**<br>- `period` (*int*): The new period (in milliseconds) between samples.

**Returns** - `DEVICE_OK` on success. Failure to set period will return `DEVICE_I2C_ERROR`.

### getPeriod(int)
```cpp
virtual int getPeriod();
```
Returns the current sample period for the compass.

**Returns** - The current sample period, in milliseconds.

### requestUpdate()
```cpp
virtual int requestUpdate();
```
{{% notice note %}}
This does not usually need to be called explicitly by the user, as sample data is typically updated passively when the scheduler is idle, however this can be used to force the compass to grab the latest available data.
{{% /notice %}}
Polls the hardware to check if new sample data is available. If so, update the stored sample data.

**Returns** - `DEVICE_OK` on success. Failure to update will return `DEVICE_I2C_ERROR`.

### update()
```cpp
virtual int update();
```
Update tick function to grab sensor data for the compass and perform gesture tracking. This is called from the scheduler as a fiber component on first call, so does not need to be explicitly repeatedly called by the user.

**Returns** - `DEVICE_OK` on success. Failure to read data will return `DEVICE_I2C_ERROR`.

### getSample(CoordinateSystem)
```cpp
Sample3D getSample(CoordinateSystem coordinateSystem);
```
Reads the last stored compass data, converting it into the provided coordinate system before returning it.

**Parameters**<br>- `coordinateSystem` (*[CoordinateSystem](/api-reference/core/coordinatespace/#coordinatesystem)*): The type of coordinate system to return the sample as.

**Returns** - A [Sample3D](/api-reference/core/sample3d/) of the most recently stored compass data.

### getSample()
```cpp
Sample3D getSample();
```
Reads the last stored compass data, returning it in the default coordinate space for this compass (constructor value `coordinateSpace`).

**Returns** - A [Sample3D](/api-reference/core/sample3d/) of the most recently stored compass data.

### getX()
```cpp
int getX();
```
Reads the X component of the last stored compass data in the default coordinate space for this compass (constructor value `coordinateSpace`).

**Returns** - The X component of the most recently stored compass data.

### getY()
```cpp
int getY();
```
Reads the Y component of the last stored compass data in the default coordinate space for this compass (constructor value `coordinateSpace`).

**Returns** - The Y component of the most recently stored compass data.

### getZ()
```cpp
int getZ();
```
Reads the Z component of the last stored compass data in the default coordinate space for this compass (constructor value `coordinateSpace`).

**Returns** - The Z component of the most recently stored compass data.


## Structs
### CompassCalibration
Represents calibration information for the compass.
- `centre`: The zero offset of the compass.
- `scale`: The scale offset to apply in each axis.
- `radius`: The field strength (units from the center to the outermost sample). 
```cpp
struct CompassCalibration
{
    Sample3D centre;
    Sample3D scale;
    int radius;

    CompassCalibration() : centre(), scale(1024, 1024, 1024)
    {
        radius = 0;
    }
};
```

## Class Definition
```cpp
class Compass : public CodalComponent
{
    public:
        Compass(CoordinateSpace &coordinateSpace, uint16_t id = DEVICE_ID_COMPASS);
        Compass(Accelerometer &accel, CoordinateSpace &coordinateSpace, uint16_t id = DEVICE_ID_COMPASS);
        int heading();
        int getFieldStrength();
        int calibrate();
        void setCalibration(CompassCalibration calibration);
        CompassCalibration getCalibration();
        int isCalibrated();
        int isCalibrating();
        void clearCalibration();
        void setAccelerometer(Accelerometer &accelerometer);
        virtual int configure();
        virtual int setPeriod(int period);
        virtual int getPeriod();
        virtual int requestUpdate();
        virtual int update();
        Sample3D getSample(CoordinateSystem coordinateSystem);
        Sample3D getSample();
        int getX();
        int getY();
        int getZ();
};
```