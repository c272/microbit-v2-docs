+++
title = "ManagedBuffer"
weight = 2
summary = "A mutable reference counted byte buffer, for general purpose use."
+++
{{% center %}}
A mutable reference counted byte buffer, for general purpose use.

**Version:** `f1dd424`<br>
**Canonical type name:** `codal::ManagedBuffer`<br>
**Defined within:** `inc/types/ManagedBuffer.h`

{{% /center %}}

## Usage & Examples
We can create a new managed buffer by simply passing in a length to the constructor. This will fill the buffer with zero by default, however you can also pass a [BufferInitialize](#bufferinitialize) strategy to simply leave the arbitrary data within the buffer if desired.
```cpp
//Create a new buffer of length 32.
ManagedBuffer buf(32);
```

Now that this is done, we can manipulate regions of the data with some of the built in helper functions, such as `fill` and `shift` as in the below examples, as well as retrieve some data for display.
```cpp
//Fill some very important bytes with data.
buf.fill(0xAA, 0, sizeof(int));

//Fill some other region, and shift it (bounded).
buf.fill(0xBB, 16, 16);
buf.shift(8, 16, 16);

//Print that very important data we saved before!
int* important_data = reinterpret_cast<int*>(buf.getBytes());
uBit.display.scroll(*important_data);
```

Buffers also support accessing via. operator overloads of the array access and assign operators, in addition to copy constructors overloading the assignment operator.
```cpp
//We can also assign things into buffers a more simple way:
buf[0] = 0xBA;
buf[1] = 0xDB;
buf[2] = 0xEE;
buf[3] = 0xF0;

//It is easy to create a new buffer referencing another!
ManagedBuffer buf_ref = buf;

//Checking equality of buffers is also possible.
//Note that this does *not* check the contents for equality, it verifies that the 
//*internal data pointer* of the two buffers are identical.
if (buf_ref == buf)
    uBit.display.scroll("Buffers equal!");
```

## Methods
### ManagedBuffer()
```cpp
ManagedBuffer();
```
Creates a empty managed buffer pointing to static empty data. This pointer is shared between all empty buffers.

### ManagedBuffer(int, ...)
```cpp
ManagedBuffer(int length, BufferInitialize initialize = BufferInitialize::Zero);
```
Creates a managed buffer of the given length, initialising the buffer to the type of initialise data specified by [`BufferInitialize`](#bufferinitialize).

**Parameters**<br>- `length` (*int*): The length of the buffer to initialise.<br>- `initialize` (*[BufferInitialize](#bufferinitialize)*, optional): The type of data to initialise the buffer with. By default, zero-initialised.

### ManagedBuffer(uint8_t*, int)
```cpp
ManagedBuffer(uint8_t *data, int length);
```
Creates a managed buffer containing the given data, with a specified length.

**Parameters**<br>- `data` (*uint8_t\**): A pointer to the start of the data to copy into the buffer.<br>- `length` (*int*): The length of the data to copy into the buffer.

### ManagedBuffer(const ManagedBuffer&)
```cpp
ManagedBuffer(const ManagedBuffer &buffer);
```
Copy constructor. Creates a managed buffer pointing as a reference to the original managed buffer passed in.

**Parameters**<br>- `buffer` (*const [ManagedBuffer](#)&*): A const reference to the managed buffer that will be pointed to by the new instance.

### ManagedBuffer(BufferData*)
```cpp
ManagedBuffer(BufferData *p);
```
Creates a buffer from raw buffer data, typically obtained from calling `leakData`, and increases the reference count of that data by one.

**Parameters**<br>- `buffer` (*[BufferData](#bufferdata)*): The raw buffer data to reference from the new managed buffer.

### initEmpty()
```cpp
void initEmpty();
```
Initialises the managed buffer as an empty buffer, pointing it to an static empty data region. This static empty data region is shared by all managed buffers.

### init(uint8_t*, int, BufferInitialize)
```cpp
void init(uint8_t *data, int length, BufferInitialize initialize);
```
Initialises the managed buffer as a new buffer with the given data of a provided length, using the initialise strategy provided by the [BufferInitialize](#bufferinitialize). If the strategy is [`BufferInitialize::Zero`](#bufferinitialize) then the data will be copied if a non-null pointer is provided, and then the memory region zeroed out (you should pass `NULL` to avoid useless copy). If the buffer strategy is [`BufferInitialize::None`](#bufferinitialize), then the data will be copied if a non-null pointer is provided.

**Parameters**<br>- `data` (*uint8_t\**): A pointer to the start of data to copy into the buffer, or `NULL`.<br>- `length` (*int*): The length of the data to copy from the data pointer.<br>- `initialize` (*[BufferInitialize](#bufferinitialize)*): The initialise strategy to use for this buffer. Can be either `None` or `Zero`.

### getBytes()
```cpp
uint8_t *getBytes();
```
Returns a pointer to the bytes currently stored in the buffer.

**Returns** - A `uint8_t` pointer to the start of buffer storage.

### getByte(int)
```cpp
int getByte(int position);
```
Retrieves the byte at the given position in the buffer's storage.

**Parameters**<br>- `position` (*int*): The zero-based index of the byte to retrieve from buffer storage.

**Returns** - If the index is valid, the byte stored at the given position. Otherwise, `DEVICE_INVALID_PARAMETER`.

### setByte(int, uint8_t)
```cpp
int setByte(int position, uint8_t value);
```
Sets the byte at the given position in the buffer's storage to the new provided value.

**Parameters**<br>- `position` (*int*): The zero-based index of the byte to replace in buffer storage.<br>- `value` (*uint8_t*): The new value to store in the buffer byte.

**Returns** - If the index is valid, the byte stored at the given position. Otherwise, `DEVICE_INVALID_PARAMETER`.

### length()
```cpp
int length() const;
```
Returns the length of the buffer, in bytes.

**Returns** - The length of the buffer, in bytes.

### fill(uint8_t, ...)
```cpp
int fill(uint8_t value, int offset = 0, int length = -1);
```
Fills a portion of the buffer (defined by the provided offset & length) with a given byte value.

**Parameters**<br>- `value` (*uint8_t*): The value to copy into the buffer section.<br>- `offset` (*int*, optional): The offset from the beginning of the buffer that the fill portion starts at.<br>- `length` (*int*, optional): The length of the fill portion. If defined as below 0, then assumes the entire length of the buffer (in this case, the offset should **always** be zero).

**Returns** - If the offset is >=0, `DEVICE_OK`. Otherwise, `DEVICE_INVALID_PARAMETER`.

### slice(...)
```cpp
ManagedBuffer slice(int offset = 0, int length = -1) const;
```
Returns a slice the current managed buffer at the provided offset, of the given length.

**Parameters**<br>- `offset` (*int*, optional): The offset from the beginning of the buffer that the new buffer starts at.<br>- `length` (*int*, optional): The length of the new buffer. If defined as below 0, then assumes the entire remaining length of the buffer.

**Returns** - If the offset is >=0, `DEVICE_OK`. Otherwise, `DEVICE_INVALID_PARAMETER`.

### shift(int, ...)
```cpp
void shift(int offset, int start = 0, int length = -1);
```
Shifts a portion of the managed buffer data by the given byte offset, bounded to the byte offset. If passed invalid start/length parameters, this method will do nothing.

{{% notice info %}}
There is currently a known bug with this function which causes any shifts larger than `length` to cause the entire buffer to be wiped to zero ([issue tracker](https://github.com/lancaster-university/codal-core/issues/156)).
{{% /notice %}}

**Parameters**<br>- `offset` (*int*): The offset to move the data by, in bytes.<br>- `start` (*int*, optional): The start location of the data to move, relative to the start of the buffer. By default, the start of the buffer.<br>- `length` (*int*, optional): The length of the data to move. By default, assumes all buffer data following the `start` index.

### rotate(int, ...)
```cpp
void rotate(int offset, int start = 0, int length = -1);
```
Rotates the bytes of a portion of the given managed buffer data by the given number of bytes. Negative offset rotates left, positive offset rotates right. If passed invalid parameters, this method will do nothing.

**Parameters**<br>- `offset` (*int*): The number of bytes to rotate the data by.<br>- `start` (*int*, optional): The start location of the data region to rotate, relative to the start of the buffer. By default, the start of the buffer.<br>- `length` (*int*, optional): The length of the data region to rotate. By default, the remaining length of the buffer following `start`.

### readBytes(uint8_t*, int, int, ...)
```cpp
int readBytes(uint8_t *dst, int offset, int length, bool swapBytes = false) const;
```
Reads a given portion of the buffer into the provided destination, reversing the order of the bytes if `swapBytes` is true.

**Parameters**<br>- `dst` (*uint8_t\**): The destination memory location to copy to.<br>- `offset` (*int*): The start location of the data region to copy, relative to the start of the buffer.<br>- `length` (*int*): The length of the data region to copy.<br>- `swapBytes` (*bool*): Whether to reverse the bytes when copying to the destination.

**Returns** - If any parameters are invalid or out of range, `DEVICE_INVALID_PARAMETER`. Otherwise, `DEVICE_OK`.

### writeBytes(int, uint8_t*, int, ...)
```cpp
int writeBytes(int dstOffset, uint8_t *src, int length, bool swapBytes = false);
```
Writes bytes from a given source location into a defined buffer region, reversing the order of the bytes if `swapBytes` is true.

**Parameters**<br>- `dstOffset` (*int*): The destination buffer offset to copy data into, relative to the start of the buffer.<br>- `src` (*uint8_t\**): The source location to copy from.<br>- `length` (*int*): The length of the data to copy.<br>- `swapBytes` (*bool*): Whether to reverse the bytes when copying into the buffer.

**Returns** - If any parameters are invalid or out of range, `DEVICE_INVALID_PARAMETER`. Otherwise, `DEVICE_OK`.

### writeBuffer(int, const ManagedBuffer&, int, ...)
```cpp
int writeBuffer(int dstOffset, const ManagedBuffer &src, int srcOffset = 0, int length = -1);
```
Writes data from another managed buffer into the provided buffer region.

**Parameters**<br>- `dstOffset` (*int*): The destination buffer offset to copy data into, relative to the start of the buffer.<br>- `src` (*const [ManagedBuffer](#)&*): The source buffer to copy from.<br>- `length` (*int*): The length of the data to copy.

**Returns** - If any parameters are invalid or out of range, `DEVICE_INVALID_PARAMETER`. Otherwise, `DEVICE_OK`.

### isReadOnly()
```cpp
bool isReadOnly() const;
```
Returns whether this buffer is read only or not.

**Returns** - Whether the buffer is read only.

### truncate(int)
```cpp
int truncate(int length);
```
Truncates the current buffer to the given length. Does not free the remaining data.

**Parameters**<br>- `length` (*int*): The new length of the buffer. Must be less than or equal to the current length of the buffer.

**Returns** - If the length is <0 or out of range, `DEVICE_INVALID_PARAMETER`. Otherwise, `DEVICE_OK`.

### leakData()
```cpp
BufferData *leakData();
```
Returns the current internal buffer data of the managed buffer, and then reinitialises the current buffer to an empty instance. Does not decrease the reference count on the returned internal buffer data.

**Returns** - The buffer's internal [BufferData](#bufferdata).

## Structs
### BufferData
Represents a reference counted byte array with a defined length.
```cpp
struct BufferData : RefCounted
{
    uint16_t length;
    uint8_t payload[0];
};
```

## Enumerations
### BufferInitialize
The available initialisation strategies for managed buffers. `None` will not perform any `memset()` after data is copied into the buffer, and will leave any arbitrary data in the buffer contents. `Zero` will set the buffer contents to 0 after data copy.
```cpp
enum class BufferInitialize : uint8_t
{
    None = 0,
    Zero
};
```

## Class Definition
```cpp
class ManagedBuffer
{
    public:
        ManagedBuffer();
        ManagedBuffer(int length, BufferInitialize initialize = BufferInitialize::Zero);
        ManagedBuffer(uint8_t *data, int length);
        ManagedBuffer(const ManagedBuffer &buffer);
        ManagedBuffer(BufferData *p);
        ~ManagedBuffer();

        void initEmpty();
        void init(uint8_t *data, int length, BufferInitialize initialize);
        uint8_t *getBytes();
        int getByte(int position);
        int setByte(int position, uint8_t value);
        int length() const;
        int fill(uint8_t value, int offset = 0, int length = -1);
        ManagedBuffer slice(int offset = 0, int length = -1) const;
        void shift(int offset, int start = 0, int length = -1);
        void rotate(int offset, int start = 0, int length = -1);
        int readBytes(uint8_t *dst, int offset, int length, bool swapBytes = false) const;
        int writeBytes(int dstOffset, uint8_t *src, int length, bool swapBytes = false);
        int writeBuffer(int dstOffset, const ManagedBuffer &src, int srcOffset = 0, int length = -1);
        bool isReadOnly() const;
        int truncate(int length);
        BufferData *leakData();

        ManagedBuffer& operator = (const ManagedBuffer& p);
        uint8_t operator [] (int i) const;
        uint8_t& operator [] (int i);
        bool operator== (const ManagedBuffer& p);
};
```