+++
title = "CoordinateSpace"
weight = 2
summary = "Representation of a coordinate space, for easy transformations in 3D space related abstractions."
+++

{{% stub %}}

## Enumerations
### CoordinateSystem
The types of coordinate system available to the accelerometer.
```cpp
enum CoordinateSystem
{
    RAW,
    SIMPLE_CARTESIAN,
    NORTH_EAST_DOWN,
    EAST_NORTH_UP,
    NORTH_EAST_UP = EAST_NORTH_UP, //For legacy compatibility.
};
```