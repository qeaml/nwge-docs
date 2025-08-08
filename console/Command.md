# Command

A [console](Console) **Command** executes some given action when being input
into the engine console. Your game may register its own console commands using a
Command [engine object](../EngineObject).

## Engine Object

You must initialize a command engine object with a name and a callback. When the
user enters the command in the console, the callback is invoked with the
space-separated arguments provided by the user. The callback can have one of 4
signatures:

1. `bool callback(const nwge::console::Args &args)`
2. `bool callback()`
3. `void callback(const nwge::console::Args &args)`
4. `void callback()`

In case of signatures 1 and 2, the callback returns a boolean result. If the
result is `false`, the engine will close your game. In most cases you'll use
either 3 or 4. `nwge::console::Args` is an alias for `nwge::ArrayView<const
StringView>`.

The command is only usable for as long as its specific engine object exists. In
case two engine objects with the same name exist, only the first one will be
used when the user executes the command. A warning is issued to the engine
journal in case this happens. You can view a list of command objects with the
`c.list` built-in console command.

The `nwge::console::Command` class is defined in `<nwge/console/Command.hpp>`
header. Command engine objects use object IDs with type `6`.

## See Also

* [Console](Console)
* [ConVar](ConVar)