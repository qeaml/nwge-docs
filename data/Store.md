# Store

A data system **store** is the primary mechanism for saving & loading data in
the nwge engine. While a [bundle](Bundle) can only be used for loading data, a
store can be used to both save and load data. This is mainly used to record
player progress in your game. A store [engine object](../EngineObject) is used
to load and save files in a store directory.

## Engine Object

You must use store objects to enqueue data to be loaded or saved into files in a
store. For this a store must be initialized in one of two ways:

* With a predetermined name, thus a named store
* Without a predetermined name, thus an unnamed store

The `nwge::data::Store` class is defined in the `<nwge/data/store.hpp>` header.
Store engine objects use object IDs with type `3`.

## Location

Stores are located within the root store directory.

* On Windows, this is the `%APPDATA%\nwge\store` directory.
* On Linux, this is the `~/.local/share/nwge` directory. If the `XDG_DATA_HOME`
  environment variable is defined, then the `$XDG_DATA_HOME/nwge` directory is
  used instead.

Then, your game will have its own subdirectory within the root store directory.
For example, Minifish on Windows will use the `%APPDATA%\nwge\store\Minifish`
directory.

Unnamed stores will use the game's directory to store files. Named stores will
create a subdirectory within the game's directory instead. For example, if a
game named "CoolGame" had a "Trophies" store, on Linux it'd be stored in the
`~/.local/share/nwge/CoolGame/Trophies` directory.

Once you're created a store object, you can use its `nqLoad` and `nqSave`
function to enqueue files within it to be loaded or saved respectively.

In case you try to load a file that does not exist, the engine will simply
ignore your request. You can use this fact to check if the file was loaded:

```c++
struct SaveData {
  bool loaded = false; // assume the file doesn't exist by default
  u32 health = 100; // default value if it doesn't exist

  // the engine will only call load() if the file exists and was successfully
  // opened for reading
  bool load(data::RW &file) {
    loaded = true;
    // ...
  }
}
```

## See Also

* [Bundle](Bundle)
