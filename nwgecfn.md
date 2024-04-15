# nwgecfn

The `nwgecfn` command-line tool is used to compile & inspect NwgeCFN fonts. It
interacts with SDL2, SDL2_ttf and libnwge_cfn directly.

Simply running the command with no arguments will print out a help message:

```console
$ nwgecfn
Usage: nwgecfn <action> [options...]
Where <action> can be:
  compile -> compile a TrueType font into a NwgeCFN font
  dump -> extract texure atlas from a NwgeCFN font
And [options...] differs from action to action, but the below are always available:
  -h, --help, -? -> show help
```

Additionally, running an action with the `-h` flag will provide help for that
action:

```console
$ nwgecfn compile -h
Usage: nwgecfn compile <TrueType font> <resolution> <NwgeCFN output font>
```
