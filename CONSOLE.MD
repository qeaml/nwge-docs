# Engine Console

The engine console allows end-users to inspect engine state and execute commands
to manipulate it. The console also contains additional information about what
the engine is doing. It can be opened by pressing <kbd>F2</kbd>.

The engine generally refrains from printing to console, preferring to use the
engine journal instead. The journal is specifically designed to be as verbose as
possible without having the app's output be lost in the sea of engine
information.

## App Commands

Your app may register a console command, which is useful for debugging release
builds. You should use your debugger instead of console commands while
developing your app, however console commands may be necessary to do some
initial research into an issue in a release build.

Currently, custom console commands are simple function pointers, meaning there
is no reliable way to pass state to them. Passing a lambda which does not have a
static lifetime will cause issues. This will be reworked in the future to
utilize the engine object system instead.

## Programming Interface

The `console.hpp` header provides you with the `print()` functions and various
overloads of it, as well as utility functions:

* `print(Color, StringView)` allows you to print an unformatted string to the
  console with the provided color. `print(StringView)` is the same, defaulting
  the color to `White`.

* `print(Color, std::format_string, ...)` allows you to print a formatted string
  to the console with the provided color. The formatting is performed via the
  C++20 formatting library. The console message, before being added to the
  console's internal buffer, is stored in scratch-space.
  `print(std::format_string, ...)` is the same, defaulting the color to `White`.

* `note(Color, std::format_string, ...)` is the same as `print`, just defaulting
  the color to `Yellow` instead.

* `warn(Color, std::format_string, ...)` is the same as `print`, just defaulting
  the color to `Yellow` instead.

* `error(Color, std::format_string, ...)` is the same as `print`, just
  defaulting the color to `Red` instead.

You may also programmatically clear the console via the `clear()` function.

You may use the `registerCommand(StringView, CmdCallback)` function to register
a custom command. You can view all registered commands via the `c.list` console
command.
