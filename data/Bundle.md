# Bundle

A data system **bundle** is the primary mechanism for storing assets in the nwge
engine. A bundle [engine object](../EngineObject) is used to load files from a
bundle file. Such bundle files are usually created using the `nwgebndl`
command-line tool provided with nwge binaries. Bundle files can also be read and
written by interfacing with the `nwge_bndl` shared library directly.

Bundles are **flat**. You cannot have directories within a bundle, only files.
The `nwgebndl` tool will emit a warning if it encounters a directory while
adding files to a bundle file.

## Engine Object

You must use bundle objects to enqueue data to be loaded from bundles into the
appropriate objects. For this, the bundle must be initialized and optionally
loaded. A bundle object can be initialized in one of two ways:

* With a predetermined name, thus a named bundle
* Without a predetermined name, thus an unnamed bundle

When you initialize a named bundle object, it is not needed to load it before
using it. When you enqueue some data to be loaded from a named bundle object,
the engine will automatically locate and load that bundle for you. This is the
preferred way to load your game's assets.

When you initialize an unnamed bundle object, you must call its `load()` method
with a file path to the bundle you wish to use. This can be useful when loading
expansion packs or user-generated content.

When a bundle is loaded into a bundle object, its file tree is internally
replicated as a `Table` for faster lookups and each individual file's contents
are loaded into memory to improve load times.

The user may overwrite files in your game's bundles via mods. *TODO: Link to a
page on mods.*

The `nwge::data::Bundle` class is defined in the `<nwge/data/bundle.hpp>`
header. Bundle engine objects use object IDs with type `4`.

## Tool

The `nwgebndl` tool is used to create and inspect bundle files directly from the
command-line.

```console
> nwgebndl
Usage:
  nwgebndl <action>
Where <action> can be:
  list - view the contents of a bundle
  extract - extract files from a bundle
  create - create a new bundle
```

To create a bundle file that contains all files from a specific folder, use the
`create` action with the path of the folder and the path of the bundle to
create:

```console
> nwgebndl create ./data ./target/data/minifish.bndl
Creating bundle ./target/data/minifish.bndl
+ FISH.OBJ
+ RODCAST.QOA
+ BOOT.PNG
+ TREE.OBJ
+ FISHRAINBOW.PNG
+ FISHSKELETON.OBJ
...
```

The `create` action also accepts an optional `ver` parameter that allows you to
specify what bundle file version to create:

```console
> nwgebndl create --ver=2 ./data ./target/data/minifish.bndl
Creating bundle ./target/data/minifish.bndl
+ FISH.OBJ
+ RODCAST.QOA
+ BOOT.PNG
+ TREE.OBJ
+ FISHRAINBOW.PNG
+ FISHSKELETON.OBJ
...
```

When you're creating a bundle, the following will **not** be included in the
resulting bundle:

* Folders
* Files beginning with an underscore (`_`) or a dot (`.`)
* Files ending with a tilde (`~`)

You can see what files are contained in a bundle file with the `list` action:

```console
> nwgebndl list ./target/data/minifish.bndl
Files in ./target/data/minifish.bndl:
  FISH.OBJ - 14888 bytes at offset 16
  RODCAST.QOA - 13608 bytes at offset 14912
  BOOT.PNG - 3976 bytes at offset 28528
  TREE.OBJ - 32642 bytes at offset 32512
  FISHRAINBOW.PNG - 1465 bytes at offset 65168
  FISHSKELETON.OBJ - 4802 bytes at offset 66640
```

The `list` action also accepts a `csv` flag that changes the output to be
CSV-formatted:

```console
> nwgebndl list -csv ./target/data/minifish.bndl
Name,Size,Offset
FISH.OBJ,14888,16
RODCAST.QOA,13608,14912
BOOT.PNG,3976,28528
TREE.OBJ,32642,32512
FISHRAINBOW.PNG,1465,65168
FISHSKELETON.OBJ,4802,66640
```

To extract files from a bundle, use the `extract` action:

```sh
nwgebndl extract ./target/data/minifish.bndl ./target/data/minifish
```

The `extract` action will extract all files by default. You can optionally
specify a regular expression to match files against, and only extract matching
files:

```console
> nwgebndl extract ./target/data/minifish.bndl ./target/data/minifish FISH.*
> ls ./target/data/minifish
FISHCLOWN.OBJ  FISHGAY.PNG   FISHLESBIAN.PNG  FISH.PNG         FISHSKELETON.OBJ  FISHTHAI.PNG
FISHCLOWN.PNG  FISHHALO.OBJ  FISH.OBJ         FISHRAINBOW.PNG  FISHSKELETON.PNG
```

## File

The file is effectively split up into 3 parts:

* Header
* File tree
* File data

The header consists of a magic number, a version number, a file tree offset and
some padding bytes:

```c
struct BundleHeader {
  uint8_t magic[7];
  uint8_t version;
  uint32_t fileTreeOffset;
  uint32_t padding;
}
```

The most current version of the bundle format is 2, but version 1 is still
supported. The `nwgebndl` tool created version 1 bundle files by default.

The file tree consists of a file count, some padding bytes and a hash of the
file tree data. In version 1, there is no hash and no padding. Afterwards, each
individual file's information is stored one after another.

```c
struct BundleFileTree {
  uint32_t fileCount;
  uint32_t padding;
  uint64_t hash;
  BundleFile files[fileCount];
}
```

Each file is defined by a 12-character name, 4-character extension, size, offset
and modification timestamp. The timestamp is not included in version 1 bundles.

```c
struct BundleFile {
  char name[12];
  char ext[4];
  uint32_t offset;
  uint32_t size;
  uint64_t timestamp;
};
```

The name of the file can be shorter than 14 characters, in which case it is
padded with zeroes. The name is stored in all uppercase, the names are not
case-sensitive. Same applies to the extension.

The file's offset may not point into any of the bundle file's structures (the
header or the file tree). Thus, an offset of 0 is not valid since it points into
the file header.

Additionally, the file's size may not extend into the bundle file's structures.
For example, if a file's data is stored directly before the file tree and it's
size is too large, then the bundle file will be rejected.

## Shared Library

The `nwge_bndl` shared library (`libnwge_bndl.so` on Linux or `nwge_bndl.dll` on
Windows) can be used to both read and write bundle files. Below is a
surface-level overview of how to interact with this library. It is recommended
you take a look at the library's header files yourself as they're well
documented.

### Reading

To read a bundle file, you must first call `bndlInitReader`:

```c
#include <nwge/bndl/reader.h>

void readingBundleFiles(SDL_RWops *bundleRW) {
  BndlReader reader;
  BndlErr err = bndlInitReader(&reader, bundleRW);
}
```

On success the function will return `BndlErrOK`. This will parse the bundle
file's header and file tree. You can check what bundle version you're loading in
the `reader.version` field. You can access the tree directly in the
`reader.tree` field.

While you could search for the wanted file yourself, you should always use
`bndlReaderFind` to match behavior of other applications:

```c
void readingBundleFiles(SDL_RWops *bundleRW) {
  // ...

  BndlFile *file = bndlReaderFind(&reader, "config.json");
}
```

In case the file wasn't found anywhere in the file (or if you specified a
filename that cannot be stored in the bundle) a null pointer is returned. You
must **always** check the return value of this function before using it.

Once you've retrieved the file information, you can finally open it for reading
with the `bndlReaderOpen` function:

```c
void readingBundleFiles(SDL_RWops *bundleRW) {
  // ...

  SDL_RWops *fileRW = bndlReaderOpen(&reader, file);
}
```

In case you provide an invalid file or a null pointer, this function will also
return a null pointer. Again, you should **always** check the return value of
this function before using it.

You can now read the file as you wish. Once you're done, simply use
`SDL_RWclose`. If you're done interacting with the bundle in general, you must
call the `bndlFreeReader` function:

```c
void readingBundleFiles(SDL_RWops *bundleRW) {
  // ...

  SDL_RWclose(fileRW);
  bndlFreeReader(&reader);
}
```

### Writing

To write a bundle file, you must first call `bndlInitWriter`. In this call you
also specify what version of bundle file you want to create. In this example
we'll use the most up-to-date version: 2.

```c
#include <nwge/bndl/writer.h>

void writingBundleFiles(SDL_RWops *bundleRW) {
  BndlWriter writer;
  BndlErr err = bndlInitWriter(&writer, bundleRW, 2);
}
```

You can add files using one of two function: `bndlWriterAdd` or
`bndlWriterAddUntimed`.

The main difference between these is that `bndlWriterAdd` accepts an extra
`modTime` argument, that corresponds to the file's modification time. Generally,
you can just retrieve this information from the operating system.

If you just wish to use the current time, simply use `bndlWriterAddUntimed`.

```c
void writingBundleFiles(SDL_RWops *bundleRW) {
  // ...

  BndlFile *file = bndlWriterAddUntimed(&writer, "config.json");
```

Once you've added the file, you can obtain an `RW` instance for it with the
`bndlWriterOpen` function:

```c
void writingBundleFiles(SDL_RWops *bundleRW) {
  // ...

  SDL_RWops *fileRW = bndlWriterOpen(&writer, file);
}
```

Once you're done with the file, close it with `SDL_RWclose`. If you're then done
writing the entire file, call the `bndlFreeWriter` function.

The `bndlFreeWriter` function will do some final housekeeping tasks, such as
writing the actual file tree into the bundle file and writing the correct file
tree offset for it in the header. Since these operation can fail, you must
**always** check the return value of this function before using your bundle
file.

```c
void writingBundleFiles(SDL_RWops *bundleRW) {
  // ...

  SDL_RWclose(fileRW);
  BndlErr err = bndlFreeWriter(&writer);
}
```

## See Also

* [Store](Store)
