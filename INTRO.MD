# Introduction to nwge

*Nwge* (for **n**e**w** **g**ame **e**ngine) is a small, light and fast game
engine written in C++.

## What does it do?

### Rendering

When it comes to rendering, nwge is effectively an abstraction layer over
OpenGL. It provides some basic primitives for idiots (like me, qeaml) to get
simple graphics via code. Every piece of OpenGL used by the engine is also
available in the public API, under the `nwge::render::gl` namespace. There is
currently no exhaustive documentation on this.

### Text rendering

When it comes to text rendering, nwge provides an efficient and idiosyncratic
font renderer.

![Screenshot of the `nwgecfn` command](img/nwgecfn.png)

*The `nwgecfn` command is used to "compile" TrueType fonts into NwgeCFN fonts.*

For more information, see the [NwgeCFN docs](CFN).

### Data loading

When it comes to data loading, nwge provides a simple bundle file format,
allowing for multiple files to be stored inside one big file. This provides some
advantages:

* Bypass the filesystem -- fast!
* Load the entire bundle into memory -- fast!
* Consistent filename handling accross platforms.

Additionally, it provides extra safety nets for how data is loaded, when it is
loaded and how data loading is communicated to the user.

There are also data stores, which allow you to store things like save files,
configuration files and other app-specific data.

![Screenshot of the `nwgebndl` command](img/nwgebndl.png)

*The `nwgebndl` command is used to create, extract and view nwge bundle files.*

For more information, see the [data system docs](DATA#bundles).

### State management

When it comes to state management, nwge provides a `State` base class, from
which you can create your own states. State transitions allow you to handle data
loading more efficiently as well as allow a quick exit in case of errors before
jumping into the gameplay loop. A sub-state system is also provided, which
allows you to run up to 16 smaller states on top of your main state. For a
clearer image of they are used for, consider the following:

* Main state - can be `TitleScreen`, `GameLoop`, `CreditsRoll` etc.
* Sub state - can be `PauseMenu`, `InventoryScreen`, `ConfirmDialog` etc.

There is currently no exhaustive information documentation on this.

### Console

When it comes to console, nwge provides an engine console which apps can use to
output more low-level information to the user. The console is the #1 way for the
user to inspect and manage app state. Apps may register a number of commands for
testing or debugging (although nothing can beat an actual debugger!).

![Console Screenshot](img/console.png)

*Console with some example commands.*

For more information, see the [console docs](CONSOLE).

### Memory allocation

When it comes to memory allocation, nwge uses [`mimalloc`][mimalloc] as it's
allocator. It is accessible via the `nwgeAlloc()`, `nwgeCalloc()` and
`nwgeRealloc()` functions as well as their variations. Additionally, nwge
provides a scratch-space, which can be used as temporary storage for various
calculations. The scratch-space is a memory arena which does not require you
free memory you allocated from but, it is very limited in size (5MB total!).

For more information, see the [memory allocation docs](MEMORY).

### Profiling

When it comes to profiling, nwge provides a minimalistic profiler allowing you
to see how much time the engine is spending on certain parts of the main loop.

![Profiler Screenshot](img/profiler.png)

*The profiler displaying frame timings & memory usage.*

There is currently no exhaustive information documentation on this.

### Configuration

When it comes to configuration, nwge is rather configurable, both for the app
developer and the end user. The developer may choose to restrict the user from
certain behaviors, while the user's configuration is always respected when not
restriced by the developer.

For more information, see the [configuration docs](CONFIG).

[mimalloc]: https://github.com/microsoft/mimalloc
