+++
title = "Using MicroBitFileSystem"
weight = 2
+++

New to the micro:bit v2 API, `MicroBitFileSystem` is a component which allows you to interact with a POSIX-like interface to store "files" on the micro:bit v2's flash memory, rather than using the older key-value file storage system from micro:bit v1, `MicroBitStorage`. This guide will discuss how to set up and use a filesystem created through this component, and some example usage for common use cases.

{{% notice info %}}
Be very careful if using this API in tandem with the old `MicroBitStorage` API. Do **not** place the key-value storage page on an intersecting page to the file system in flash memory. For example, creating both starting at the page following `FLASH_PROGRAM_END` (this is the default for the `MicroBitFileSystem` constructor) will result in overwrites, undefined behaviour, and absolutely nothing good.
{{% /notice %}}

## Creating a File System
To create a file system, we should initialise an instance of `MicroBitFileSystem` somewhere close to startup, and where it won't be destroyed by scope cleanup. Note that you should only ever have a single instance of `MicroBitFileSystem` created at any one time, so don't go and create an instance everywhere you need one. Instead, use the static property `MicroBitFileSystem::defaultFileSystem`, which is the singleton pointer that points to the first created instance of the file system class, to access it from across your project.

{{< tabs groupId="filesystem-cpp" >}}
{{% tab name="main.cpp" %}}
```cpp
#include <MicroBit.h>
#include <MicroBitFileSystem.h>

MicroBit uBit;

//Create the file system here.
MicroBitFileSystem fileSystem;

int main()
{
    //Do something.
    //...
}
```
{{% /tab %}}
{{% tab name="other_file.cpp" %}}
```cpp
#include <MicroBitFileSystem.h>

void some_other_function()
{
    //Access the file system!
    auto fileSystem = MicroBitFileSystem::defaultFileSystem;
    fileSystem->open(...);
}
```
{{% /tab %}}
{{< /tabs >}}

## Interacting with Files
### Creating & Writing to Files
Files on the `MicroBitFileSystem` can be interacted with through the class' POSIX-like file interface. This pattern will be familiar to you if you've used the `stdio.h` interface, and works in much the same way. To create a file, we can simply call the `open()` method with the `MB_CREAT` flag. If you wish to write to the file after creation with the same file handle, you should also pass the `MB_WRITE` flag. Flags can be combined with the binary OR operator.
```cpp
//Create & open write to a new file.
int fd = fileSystem.open("test.txt", MB_CREAT | MB_WRITE);
if (fd < 0) {
    //Something terrible happened!
    //...
}
```
To then write to this file we have created, we can use the `write()` method. This takes in the file descriptor we have just received, as well as the location of a buffer containing the data to write, and the length of that data. For this example, we'll simply write some text along with a null terminator.
```cpp
//Write some content out to that file.
//std::string.length() returns length *without* the null terminator, so we add 1 byte here.
std::string myText = "Hello filesystem!";
fileSystem.write(handle, (uint8_t*)myText.c_str(), myText.length() * sizeof(char) + 1);
```

Once we're done writing, this does not actually guarantee that all of our content is saved to the file system! To ensure this, we either need to call `flush()` to flush any buffer contents out to file, or simply the `close()` method to close the file descriptor and save any remaining buffered content.
```cpp
fileSystem.close(handle);
```

If you are finished with a file, and wish to delete it, you can do so with the `remove()` method. This will attempt to remove the file from the file system, returning `MICROBIT_INVALID_PARAMETER` if the file does not exist, and `MICROBIT_CANCELLED` if something unexpected went wrong.
```cpp
if (!fileSystem.remove("test.txt")) {
    //The file removal failed for some reason.
    //...
}
```

### Reading Files
You can read files created on the `MicroBitFileSystem` by opening a file descriptor with the `MB_READ` flag and using the `read()` method, like so:
```cpp
//Open a read mode file descriptor.
int fd = fileSystem.open("test.txt", MB_READ);
if (fd < 0) {
    //Something terrible happened!
    //...
}

//Read our text from file.
char buffer[100];
if (fileSystem.read(fd, buffer, 100) <= 0) {
    //We ran into an issue reading any characters.
    //...
}
```
If you need to read from a specific point in the file, or jump around and read from different places, you can use the `seek()` method to achieve this. This function takes three parameters: the file desriptor, the offset to jump, and the starting point to jump from. These starting points are:
- `MB_SEEK_SET`, the start of the file.
- `MB_SEEK_CUR`, the current location in the file. On creation of a file descriptor, this is 0.
- `MB_SEEK_END`, the end of the file.

You can use negative or positive offset with `seek()` to jump from these points to the desired location in your file.
```cpp
fileSystem.seek(fd, -9, MB_SEEK_END); //Seek to 10 bytes from the end of the file.
fileSystem.seek(fd, 32, MB_SEEK_SET); //Seek to 32 bytes from the start of the file.
fileSystem.seek(fd, 4, MB_SEEK_SET); //Seek 4 bytes forward from the current position.
```

### Directory Management
`MicroBitFileSystem` allows for the use of directories in addition to normal files. These directories, much like in `ext4` and other Unix filesystems, are simply directory entries which can be deleted with `remove()`. To create a new directory, you can call the file system's `createDirectory()` method, like so:
```cpp
//Create a top-level directory.
if (!fileSystem.createDirectory("mydirectory")) {
    //Failed to create directory.
    //...
}

//Create a nested directory.
fileSystem.createDirectory("mydirectory/subdirectory");
```

Once this directory is created, you can access files within it by using Unix-style path strings (i.e., using '/' as the path separator). This must be a canonical path with no prepending '/'.
```cpp
//Create a file inside a directory.
int fd = fileSystem.open("mydirectory/test.txt", MB_CREAT | MB_WRITE);
...
```