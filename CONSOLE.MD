# Engine Console

The engine console allows end-users to inspect engine state and execute commands
to manipulate it. The console also contains additional information about what
the engine is doing. It can be opened by pressing <kbd>F2</kbd>.

The engine generally refrains from printing to console, preferring to use the
engine journal instead. The journal is specifically designed to be as verbose as
possible without having the app's output be lost in the sea of engine
information.

## Command Syntax

In earlier versions of nwge, command syntax was as simple as "split on spaces".
This has since been improved to support other whitespace as well as arguments
containing spaces.

Using the [`e.bind`](#ebind) command as an example:

```console
> e.bind fwd
Key binding `fwd` is bound to w.
> e.bind "stop sprint"
Key binding `stop sprint` is bound to backspace.
> 	e.bind	"stop sprint"	tab
Key binding `stop sprint` bound to `tab`.
```

## App Commands

Your app may register a console command, which is useful for debugging release
builds. You should use your debugger instead of console commands while
developing your app, however console commands may be necessary to do some
initial research into an issue in a release build.

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
a custom command.

## Commands

Below is an explanation of every built-in console command.

### Engine commands (`e.*`)

#### `e.bind`

1. View a key binding: `e.bind <key binding>`
2. Change a key binding: `e.bind <key binding> <key name>`
3. View what a key is bound to: `e.bind <key name>`

Note that standard command syntax applies here: in case a key binding has a
space in its name, simply quote the name:

```console
e.bind "some key binding" space
```

#### `e.bindList`

View a list of all current key bindings.

#### `e.docs`

Open the [nwge documentation] in your web browser.

#### `e.help`

Display a help message listing all engine commands.

#### `e.journal`

Open the Journal window. See [Journal]. You can also press <kbd>F12</kbd> to
dump the journal if the console is not available.

#### `e.objects`

View the current engine object counters. See [Engine Objects].

#### `e.path`

View the current engine paths.

#### `e.profiler`

Open the Profiler window.

#### `e.quit`

Quit the app. This is equivalent to closing the window. This can also be
achieved by pressing <kbd>Shift</kbd>+<kbd>F12</kbd> if the console is not
available.

#### `e.scratch`

View scratch-space usage statistics. See [Scratch-space].

#### `e.segFault`

Intentionally access invalid memory.

#### `e.testAssert`

Intentionally cause an assertion failure. This can also be achieved by pressing
<kbd>Ctrl</kbd>+<kbd>F12</kbd> if the console is not available.

#### `e.timeScale`

Usage: `e.timeScale <scale in range 0.0-1.0>`

Change the time scale.

#### `e.ver`

View engine version.

[nwge documentation]: README
[Journal]: JOURNAL
[Engine Objects]: OBJECTS
[Scratch-space]: MEMORY#scratch-space

### Renderer commands (`r.*`)

#### `r.fps`

1. Set the FPS limit automatically based on display's refresh rate: `r.fps auto`.
2. Set the FPS limit manually: `r.fps <max FPS> <low FPS>`

    Where `low FPS` is the FPS limit used when the window is not in focus, e.g.
    minimized.

#### `r.fullscreen`

Toggle fullscreen mode. This can also be achieved by pressing <kbd>F11</kbd>.

#### `r.info`

View information about the renderer and renderer object counters.

#### `r.wireframe`

Toggle wireframe mode.

#### `r.windowSize`

1. View current window size: `r.windowSize`
2. Change window size: `r.windowSize <width> <height>`

    Only works if the window is resizable and not borderless.

### Console management commands (`c.*`)

#### `c.clear`

Clear the console.

#### `c.echo`

Print whatever arguments are provided to the console.
