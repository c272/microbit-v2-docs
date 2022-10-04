+++
title = "LEDMatrix"
weight = 2
summary = "A software representation of an LED matrix, also used to represent the micro:bit's LED display."
+++
{{% center %}}
A software representation of an LED matrix, used to represent the micro:bit's LED display in [MicroBitDisplay](/api-reference/microbit-v2/microbitdisplay/).

**Version:** `f1dd424`<br>
**Canonical type name:** `codal::LEDMatrix`<br>
**Defined within:** `inc/drivers/LEDMatrix.h`

{{% /center %}}

## Usage & Examples
This set of examples uses the [MicroBitDisplay](/api-reference/microbit-v2/microbitdisplay/) instance provided by [MicroBit](/api-reference/microbit-v2/microbit/), however these examples apply to all LEDMatrix instances. One main use case for interacting with LEDMatrix methods is to alter the rotation of the display. We can do this by passing a [DisplayRotation](#displayrotation) like so:
```cpp
//Change the rotation of the display.
uBit.display.rotateTo(DisplayRotation::MATRIX_DISPLAY_ROTATION_90);
```

Since the [MicroBit](/api-reference/microbit-v2/microbit/) class automatically initialises the display, which we may not want to use and instead utilise the edge GPIO pins that it normally occupies, we can simply disable the display after initialising to be able to use both [MicroBit](/api-reference/microbit-v2/microbit/) and also those pins simultaneously.
```cpp
uBit.init();
uBit.display.disable();
```

## Methods
### LEDMatrix(const MatrixMap&, ...)
```cpp
LEDMatrix(const MatrixMap &map, uint16_t id = DEVICE_ID_DISPLAY);
```
Creates a new LED matrix representation given the layout of the matrix (as determined by the [MatrixMap](#matrixmap)), and the event bus ID to use for this display.

**Parameters**<br>- `map` (*const [MatrixMap](#matrixmap)&*): The layout of the LED matrix.<br>- `id` (*uint16_t*, optional): The event bus ID to use for this display. By default, this is `DEVICE_ID_DISPLAY`.

### periodicCallback()
```cpp
virtual void periodicCallback();
```
Function that is called at a regular interval to strobe the display. Typically there is no need for this to be called manually by the user.

### setDisplayMode(DisplayMode)
```cpp
void setDisplayMode(DisplayMode mode);
```
Sets the current mode of the display to the given [DisplayMode](#displaymode).

**Parameters**<br>- `mode` (*[DisplayMode](#displaymode)*): The new display mode of the display.

### getDisplayMode()
```cpp
int getDisplayMode();
```
Returns the current [DisplayMode](#displaymode) of the display as an integer.

**Returns** - The current [DisplayMode](#displaymode) of the display, as an integer.

### rotateTo()
```cpp
void rotateTo(DisplayRotation position);
```
Alters the current rotation of the display to the given [DisplayRotation](#displayrotation).

**Parameters**<br>- `position` (*[DisplayRotation](#displayrotation)*): The new display rotation of the display.

### enable()
```cpp
virtual void enable();
```
Enables the LED matrix display. Should only be called if the display's state is currently "disabled".

### disable()
```cpp
virtual void disable();
```
Disables the LED matrix display, releasing the edge GPIO pins utilised by the display. This allows those pins to then be used as normal edge connectors.

### clear()
```cpp
void clear();
```
Clears the display of any 'pixels' currently present.

### setBrightness(int)
```cpp
virtual int setBrightness(int b);
```
Sets the brightness of the display. Should be within the range of 0-255.

**Parameters**<br>- `b` (*int*): The new brightness of the display, from 0-255.

**Returns** - If the brightness value is invalid, `DEVICE_INVALID_PARAMETER`. Otherwise, `DEVICE_OK`.

## Structs
### MatrixMap
Represents a map of pins. Used to determine the layout of the LED display matrix on the micro:bit.
```cpp
struct MatrixMap
{
    int width;                 // The physical width of the LED matrix, in pixels.
    int height;                // The physical height of the LED matrix, in pixels.
    int rows;                  // The number of drive pins connected to LEDs.
    int columns;               // The number of sink pins connected to the LEDs.

    Pin **rowPins;             // Array of pointers containing an ordered list of pins to drive.
    Pin **columnPins;          // Array of pointers containing an ordered list of pins to sink.

    const MatrixPoint *map;    // Table mapping logical LED positions to physical positions.
};
```

### MatrixPoint
Represents a single point on the LED matrix.
```cpp
struct MatrixPoint
{
    uint8_t x;
    uint8_t y;
};
```

## Enumerations
### DisplayMode
Available modes for LED displays. For displays with only "on or off" brightness settings, `DISPLAY_MODE_BLACK_AND_WHITE` is used. For displays with multiple levels of brightness configuration, `DISPLAY_MODE_GREYSCALE` is used. The `LIGHT_SENSE` appended variants of these will also cause the display to emit `LED_MATRIX_EVT_LIGHT_SENSE` events upon a single strobe pass being completed.
```cpp
enum DisplayMode {
    DISPLAY_MODE_BLACK_AND_WHITE,
    DISPLAY_MODE_GREYSCALE,
    DISPLAY_MODE_BLACK_AND_WHITE_LIGHT_SENSE,
    DISPLAY_MODE_GREYSCALE_LIGHT_SENSE
};
```

### DisplayRotation
Available rotations for display matrices, in degrees.
```cpp
enum DisplayRotation {
    MATRIX_DISPLAY_ROTATION_0,
    MATRIX_DISPLAY_ROTATION_90,
    MATRIX_DISPLAY_ROTATION_180,
    MATRIX_DISPLAY_ROTATION_270
};
```

## Class Definition
```cpp
class LEDMatrix : public Display
{
    public:
        LEDMatrix(const MatrixMap &map, uint16_t id = DEVICE_ID_DISPLAY);
        virtual void periodicCallback();
        void setDisplayMode(DisplayMode mode);
        int getDisplayMode();
        void rotateTo(DisplayRotation position);
        virtual void enable();
        virtual void disable();
        void clear();
        virtual int setBrightness(int b);
};
```