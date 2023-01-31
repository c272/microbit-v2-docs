+++
title = "KeyValueStorage"
weight = 2
summary = "A simple key-value storage medium implemented with flash as the backing store. Utilises an in-memory scratch buffer."
+++
{{% center %}}
A simple key-value storage medium implemented with flash as the backing store. Utilises an in-memory scratch buffer.

**Version:** `fb16846`<br>
**Canonical type name:** `codal::KeyValueStorage`<br>
**Defined within:** `inc/drivers/KeyValueStorage.h`

{{% /center %}}

## Usage & Examples
A common valid use case for key value storage may be to store configuration data for the application which is persistent over multiple power cycles. We can initialise some default values on startup, and then update them as the user requests while keeping them persistent across runs. For this example, we'll use the KeyValueStorage created by [MicroBit](/api-reference/microbit-v2/microbit/), however this would be the same on a separate instance.
```cpp
//Have we already initialised the config?
if (uBit.storage.get("initialised") == NULL) {
    //No, create default values.
    uint8_t defaultConfigValue = 21;
    uint8_t magic = 0xE770;
    uBit.storage.put("initialised", &magic, sizeof(magic));
    uBit.storage.put("someConfigValue", &defaultConfigValue, sizeof(defaultConfigValue));
}

...

//Update the config value if button pressed.
if (uBit.buttonA.isPressed()) {
    uint8_t newValue = uBit.storage.get("someConfigValue")->value[0] + 10;
    uBit.storage.put("someConfigValue", &newValue, sizeof(newValue));
}
```
In the above example, we're checking whether a key value pair "initialised" is present on the storage, and if not, initialising our default values. Of course, in these default values, we must also create "initialise", otherwise this would run on every startup. We also create a key value pair with key "someConfigValue", for later editing. In another part of our code, we can then simply update the value with `put` (unlike dictionaries in most languages, you do not `get` and then edit a reference, instead simply `push` the same key again to update).

## Methods
### KeyValueStorage(NVMController&, ...)
```cpp
KeyValueStorage(NVMController& controller, int pageNumber = DEVICE_KEY_VALUE_STORE_OFFSET);
```
Creates an instance of KeyValueStorage using the given non-volatile backing storage, starting at the provided logical page number. You can also use negative numbers for this parameter to count back from the last page. The default value is `DEVICE_KEY_VALUE_STORE_OFFSET`, which evaluates to `-4` (four pages from the end of flash).

**Parameters**<br>- `controller` (*[NVMController](/api-reference/core/nvmcontroller/)&*): The non-volatile storage controller to use.<br>- `pageNumber` (*int*, optional): The logical page number at which the key value storage should start.

### put(const char*, uint8_t*, int)
```cpp
int put(const char* key, uint8_t* data, int dataSize);
```
Places data onto the key value storage medium using a C-style constant string as the key. Data must be 32 bytes or smaller, key must be 16 bytes or shorter (including null terminator).

**Parameters**<br>- `key` (*const char\**): The key to use for this key-value pair, as a C-style string constant.<br>- `data` (*uint8_t\**): A pointer to the data that will be copied into the key value storage.<br>- `dataSize` (*int*): The size of the data to be copied into the key value storage.

**Returns** - On success, returns `KEY_VALUE_OK`. If the key or data sizes are too large, `KEY_VALUE_INVALID_PARAMETER` will be returned. If the storage page is full, `KEY_VALUE_NO_RESOURCES` will be returned.

### put(ManagedString, uint8_t*, int)
```cpp
int put(ManagedString key, uint8_t* data, int dataSize);
```
Places data onto the key value storage medium using a CODAL [ManagedString](/api-reference/core/managedstring/) as the key. Data must be 32 bytes or smaller, key must be 16 bytes or shorter (including null terminator).

**Parameters**<br>- `key` (*[ManagedString](/api-reference/core/managedstring/)*): The key to use for this key-value pair, as a CODAL string.<br>- `data` (*uint8_t\**): A pointer to the data that will be copied into the key value storage.<br>- `dataSize` (*int*): The size of the data to be copied into the key value storage.

**Returns** - On success, returns `KEY_VALUE_OK`. If the key or data sizes are too large, `KEY_VALUE_INVALID_PARAMETER` will be returned. If the storage page is full, `KEY_VALUE_NO_RESOURCES` will be returned.

### get(const char*)
```cpp
KeyValuePair* get(const char* key);
```
Returns the key value pair which contains a matching key to the C-style string passed in.

**Parameters**<br>- `key` (*const char\**): The C-style string key of the key value pair to retrieve.

**Returns** - If the key is found, a pointer to a heap allocated [KeyValuePair](#keyvaluepair). Otherwise, `NULL`.

### get(const char*)
```cpp
KeyValuePair* get(ManagedString key);
```
Returns the key value pair which contains a matching key to the CODAL [ManagedString](/api-reference/core/managedstring/) passed in.

**Parameters**<br>- `key` (*[ManagedString](/api-reference/core/managedstring/)*): The CODAL string key of the key value pair to retrieve.

**Returns** - If the key is found, a pointer to a heap allocated [KeyValuePair](#keyvaluepair). Otherwise, `NULL`.

### remove(const char*)
```cpp
int remove(const char* key);
```
Removes the key value pair containing a matching key to the provided C-style string from the key value storage.

**Parameters**<br>- `key` (*const char\**): The C-style string key of the key value pair to remove.

**Returns** - If the key is found and removed, `KEY_VALUE_OK`. Otherwise, `KEY_VALUE_NO_DATA`.

### remove(ManagedString)
```cpp
int remove(ManagedString key);
```
Removes the key value pair containing a matching key to the provided CODAL [ManagedString](/api-reference/core/managedstring/) from the key value storage.

**Parameters**<br>- `key` (*[ManagedString](/api-reference/core/managedstring/)*): The CODAL string key of the key value pair to remove.

**Returns** - If the key is found and removed, `KEY_VALUE_OK`. Otherwise, `KEY_VALUE_NO_DATA`.

### size()
```cpp
int size();
```
Returns the number of entries currently stored in the key value storage.

**Returns** - The number of entries currently in the key value store.

### wipe()
```cpp
int wipe();
```
Erases the current contents of the key value store.

**Returns** - `DEVICE_OK`.

## Structs
### KeyValuePair
Represents a single key value pair within the key value storage. Maximum sizes of each element are stated as preprocessor defines, and currently evaluate to a limit of 16 bytes for the key and 32 bytes for the value.
```cpp
struct KeyValuePair
{
    uint8_t key[KEY_VALUE_STORAGE_KEY_SIZE]; //key[16]
    uint8_t value[KEY_VALUE_STORAGE_VALUE_SIZE]; //value[32]
};
```

## Class Definition
```cpp
class KeyValueStorage
{
    public:
        KeyValueStorage(NVMController& controller, int pageNumber = DEVICE_KEY_VALUE_STORE_OFFSET);
        int put(const char* key, uint8_t* data, int dataSize);
        int put(ManagedString key, uint8_t* data, int dataSize);
        KeyValuePair* get(const char* key);
        KeyValuePair* get(ManagedString key);
        int remove(const char* key);
        int remove(ManagedString key);
        int size();
        int wipe();
};
```