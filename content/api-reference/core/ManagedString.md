+++
title = "ManagedString"
weight = 2
summary = "A copy assignable, immutable string class implemented using basic reference counting."
+++
{{% center %}}
A copy assignable, immutable string class implemented using basic reference counting.

**Version:** `a12d371`<br>
**Canonical type name:** `codal::ManagedString`<br>
**Defined within:** `inc/types/ManagedString.h`

{{% /center %}}

## Usage & Examples
Using managed strings allows us to perform all the normal operations you would expect from modern string manipulation in C++, while maintaining immutability and the ability to copy assign. The below example showcases some of the operators that can be used when interacting with managed strings.
```cpp
//Create some managed strings.
auto marco = ManagedString("Marco!");
auto polo = ManagedString("Polo!");
auto rolo = ManagedString("Rolo!");

//Scroll some text with these strings.
auto marcopolo = marco + ' ' + polo;
uBit.display.scroll(marcopolo); //Marco! Polo!

//Perform some comparisons.
if (polo != rolo) {
    uBit.display.scroll("Oh no!");
}

//Copy assignment.
rolo = polo;
auto polorolo = polo + ' ' + rolo;
uBit.display.scroll(polorolo); //Polo! Polo!
```

## Methods
### ManagedString(StringData*)
```cpp
ManagedString(StringData *ptr);
```
Creates a managed string from the raw string data of another managed string, obtained by calling `leakData()`.

**Parameters**<br>- `ptr` (*[StringData](#stringdata)\**): The raw string data pointer to copy into the new managed string.

### ManagedString(const char*)
```cpp
ManagedString(const char *str);
```
Creates a managed string from a C-style string literal.

**Parameters**<br>- `str` (*const char\**): The C-style string literal to copy into the new managed string.

### ManagedString(const int)
```cpp
ManagedString(const int value);
```
Creates a managed string from the given integer.

**Parameters**<br>- `value` (*const int*): The integer to represent in the new managed string.

### ManagedString(const char)
```cpp
ManagedString(const char value);
```
Creates a managed string from the given character.

**Parameters**<br>- `value` (*const char*): The character to copy into the new managed string.

### ManagedString(ManagedBuffer)
```cpp
ManagedString(ManagedBuffer buffer);
```
Creates a managed string from the current data in the provided [ManagedBuffer](/api-reference/core/managedbuffer/).

**Parameters**<br>- `buffer` (*[ManagedBuffer](/api-reference/core/managedbuffer/)*): The managed buffer to read string data from.

### ManagedString(const char*, int16_t)
```cpp
ManagedString(const char *str, const int16_t length);
```
Creates a managed string from the provided C-style string, copying the given length into the new string.

**Parameters**<br>- `str` (*const char\**): The C-style string to copy from.<br>- `length` (*int16_t*): The length to copy from the C-style string into the new managed string.

### ManagedString(const ManagedString&)
```cpp
ManagedString(const ManagedString &s);
```
Creates a managed string from the provided existing managed string. Note that this **does not** create copies of the internal buffer or reference count, so this is entirely a reference copy of the original string.

**Parameters**<br>- `s` (*[ManagedString](#)*): The managed string to reference copy.

### ManagedString()
```cpp
ManagedString();
```
Creates an empty managed string, identical to the static property `EmptyString`.

### leakData()
```cpp
StringData *leakData();
```
Similar in function to `std::unique_ptr<T>::move`. Returns the current [StringData](#stringdata) of the string without decrementing the reference count, and then sets the current instance to an empty string.

**Returns** - The [StringData](#stringdata) contained in the managed string.

### substring(int16_t, int16_t)
```cpp
ManagedString substring(int16_t start, int16_t length);
```
Creates and returns a new managed string from a given substring of the current instance.

**Parameters**<br>- `start` (*int16_t*): The zero-based index the substring should begin at, inclusive.<br>- `length` (*int16_t*): The number of characters in the substring.

**Returns** - A [ManagedString](#) containing the requested substring.

### charAt(int16_t)
```cpp
char charAt(int16_t index);
```
Returns the character at the given zero-based index in the string.

**Parameters**<br>- `index` (*int16_t*): The zero-based index of the requested character.

**Returns** - The character at the given index in the string.

### toCharArray()
```cpp
const char *toCharArray() const;
```
Returns the current managed string as a C-style string pointer. Be careful if storing this pointer for later use, this is simply a pointer to the internal data of the managed string, so when the managed string is destroyed this pointer will become invalid.

**Returns** - A C-style string pointer to this managed string's data.

## Static Members
### EmptyString
```cpp
static ManagedString EmptyString;
```
A static managed string that represents an empty string.

## Structs
### StringData
Holds the internal data of the managed string as a reference counted struct.
```cpp
struct StringData : RefCounted
{
    uint16_t len;
    char data[0];
};
```

## Class Definition
```cpp
class ManagedString
{
    public:
        static ManagedString EmptyString;

        ManagedString(StringData *ptr);
        ManagedString(const char *str);
        ManagedString(const int value);
        ManagedString(const char value);
        ManagedString(ManagedBuffer buffer);
        ManagedString(const char *str, const int16_t length);
        ManagedString(const ManagedString &s);
        ManagedString();

        StringData *leakData();
        ManagedString substring(int16_t start, int16_t length);
        char charAt(int16_t index);
        const char *toCharArray() const;
        int16_t length() const;

        ManagedString& operator = (const ManagedString& s);
        bool operator== (const ManagedString& s);
        bool operator!= (const ManagedString& s);
        bool operator< (const ManagedString& s);
        bool operator> (const ManagedString& s);
        friend ManagedString (codal::operator+) (const ManagedString& lhs, const ManagedString& rhs);
};
```