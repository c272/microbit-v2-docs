+++
title = "Pin"
weight = 2
summary = "Abstraction for a simple generic pin, with ability for handling both digital and analogue I/O."
+++

{{% stub %}}

## Enumerations
### PullMode
Available pullup/pulldown configurations of a pin.
```cpp
enum class PullMode : uint8_t
{
    None = 0,
    Down,
    Up
};
```