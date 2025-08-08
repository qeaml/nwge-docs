# Console

The engine **console** is used to run commands to interact with the game and/or
engine. It is accessible in any nwge game with the <kbd>F2</kbd> key.

## Usage

Console commands are often used to inspect or change engine state and are often
used for debugging in games. The engine generally does not print anything to the
console, using the journal instead.

Whenever you run a command, it is saved to a command history. You can press the
Up and Down arrow keys to navigate through it.

## Syntax

To execute a command, simply input its name with space-separated arguments. For
example:

```console
r.textures d
```

Will execute the `r.textures` command with 1 argument: `d`.

You may also wrap an argument in quotation marks (`"`) to include spaces in the
argument. For example:

```console
e.bind "key binding with spaces" tab
```

Will execute `e.bind` with 2 arguments: `key binding with spaces`, `tab`.

## API

The following functions can be used by your game to interact with the engine
console. They are defined in the `<nwge/console.hpp>` header and are defined in
the `nwge::console` namespace.

* `clear()`: clear the console
* `nextLine()`: effectively outputs a single blank line to the console
* `print()`: print 1 line of text to the console. This function has many
  overloads with different characteristics:
    - `print(StringView)`: print the line of text in white
    - `print(Color, StringView)`: print the line of text in the given color
    - `print(std::formatted_string, ...)`: print a line of formatted text in
      white. This uses the [C++20 formatting][cpp20-formatting] library.
    - `print(Color, std::formatted_string, ...)`: same as above but with
      provided color.
* `note()`: print 1 line of text in magenta, has an overload allowing for
  formatting alike `print()`
* `warn()`: like `note()` but yellow
* `error()`: like `note()` buy red

## Commands

Below is a list of console commands pertaining to the console itself:

* `c.help` shows you a list of the below commands.
* `c.echo` will output the arguments you pass to it. This can be used to explore
  how the console parses command arguments.
* `c.list` will output list all [command](Command) [engine
  object](../EngineObject)s currently registered by the game.

## See Also

* [Command](Command)

[cpp20-formatting]: https://en.cppreference.com/w/cpp/header/format.html
