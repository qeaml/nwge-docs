# Nwge Data System

This is a detailed overview of how nwge's data system works. From the core data
loading loop to bundle and data store management.

It is recommended you get a grasp on [Engine Objects](OBJECTS) before reading.

## Table of Contents

* [Nwge Data System](#nwge-data-system)
  * [Table of Contents](#table-of-contents)
  * [Queues](#queues)
  * [When is data loaded?](#when-is-data-loaded)
  * [Blocking loads](#blocking-loads)
  * [Load priority](#load-priority)
  * [Bundles](#bundles)
  * [Data stores](#data-stores)
  * [Data types](#data-types)

## Queues

All the data that the an app wishes to load must be enqueued first. The exact
place where the data is enqueued decides how the loading process in handled.
This mostly depends on which `State` method the data was enqueued within and the
`LoadBlocking` and `LoadPriority` settings.

Internally, there are 8 queues in the data system:

1. Bundle loading queue
2. Font loading queue
3. Texture loading queue
4. Script loading queue
5. Custom data loading queue
6. Data store loading queue
7. Data store saving queue

These queues are handled in this exact order. Every queue is limited to 16 items
each, but you should generally never reach this limit.

Previous versions of nwge also had a string queue, allowing for text data to
be loaded from files. You should use `Bundle::nqCustom` with a function
pointer to replicate this behavior -- preferably reading directly into the
desired output object.

## When is data loaded?

> Although the term load/loading is used, this also refers to saving data to a
> [store](#data-stores).

Before ticking your state, the engine checks whether you enqueued any data on
the previous tick. If you have, then it will load 1 enqueued item per frame.
This means that enqueuing 15 items will cause 15 frames worth of loading time.
Note that due to this, given default engine options, if data is enqueued in the
`State::preload()` method, the `State::init()` method will only be called after
all that data is loaded.

## Blocking loads

When the engine performs a blocking load, a loading screen is displayed and the
app state is **not** ticked or rendered. Which loads are considered to be
blocking is determined by the `LoadBlocking` engine option:

1. `Never`: all loads are non-blocking
2. `Auto`: automatically chosen depending on current state. See below.
3. `Manual`: managed by app using `enableBlockingLoad()` and
   `disableBlockingLoad()`
4. `Always`: all loads are blocking

For the `Auto` setting, the engine checks whether we have called the main
state's `init()` method yet. If the `init()` method hasn't been called yet, we
perform a blocking load. We perform non-blocking loads otherwise. This is the
default setting.

## Load priority

What happens when we fail to load something? That is decided by the
`LoadPriority` engine option:

1. `Low`: all errors are ignored
2. `Auto`: automatically chosen depending on load blocking. See below.
3. `High`: all errors cause the engine to crash

For the `Auto` setting, the engine checks whether the load was a blocking load.
If it was, then the engine will crash upon error. Otherwise all errors are
ignored. This is the default setting.

## Bundles

Bundles are used to store multiple files in one file. This allows the engine to:

1. Skip the filesystem entirely
2. Keep all the file contents in memory
3. Ensure consistent filename handling on different platforms & filesystems.

Both of these improve data loading performance. (especially if you have a
slow HDD!)

You can learn more about bundle files in the [bundle file docs](BUNDLE).

The app-facing Bundle interface is based around engine objects. That means the
bundles can be copied and moved freely, as they are internally reference
counted.

There are two ways to load a bundle:

1. Load a named bundle through the `Bundle(StringView)` constructor
2. Load from a path using the `Bundle::load(Path)` method

In case of a named bundle, it will be loaded when the first data
item from it is enqueued. In case of a bundle with an explicit path, it is
enqueued the moment `load()` is called.

For a more low-level interface over bundle files, see the `nwge_bndl` library.

## Data stores

Data stores are used to read and write app-specific data. For save files or
configuration, for example. Data stores are engine objects in much the same way
as bundles. Each data store corresponds 1:1 to a directory on-disk inside nwge's
store directory. Data stores may be **shared**, which means multiple different
apps have access to the same exact data store. Unlike regular data stores,
shared data stores *must* have a name. Below is an explanation of how different
directories are found:

If we assume that nwge's home directory is `%APPDATA%\nwge`, then:

* The **root store directory** is `%APPDATA%\nwge\store`.
* The **shared store directory** is `%APPDATA%\nwge\shared-store`.
* The **app's store directory** is `%APPDATA%\nwge\store\<app name>`.
* A **named store's directory** is `%APPDATA%\nwge\store\<app name>\<store
  name>`.
* An **unnamed store's directory** is the same as the **app's store directory**.

> `%APPDATA%\nwge` is the default **nwge home directory** on Windows. On Linux,
> the default home directory is `~/.local/share/nwge` instead. You may set your
> own home directory by using the `NWGE_HOME` environment variable on either
> platform.

As we can conclude from above, a store may or may not have a name. Stores cannot
be loaded from a path directly.

You can see the exact paths the engine uses with the `e.paths` console command.

## Data types

As you read in [Queues](#queues) above, the engine has a "custom data" queue.
This allows you to load custom data types from your bundle files.

This is facilitated via `Loadable`s. A `Loadable` is any object that defines a
`load()` method, with the following prototype:

```c++
bool load(nwge::data::RW &file);
```

This method must load the data from the file into the object it was called on.
If the data is loaded successfully, return `true`. If an error occurs while
loading the data, use the `nwge::dialog::error` function to report it and return
`false` to let the engine handle the failure.

Alternatively, the engine provides the ability to simply pass a function to call
with the file instead.

An example of a custom loader for sprite sheet files:

```c++
bool SpriteSheet::load(nwge::data::RW &file) {
  // ... load sprite sheet from file ...
  return true;
}

bool MyState::preload() {
  // the engine will call mSpriteSheet's load() method with the file
  mBundle.nqCustom("SOME.SPRITE", mSpriteSheet);
}
```
