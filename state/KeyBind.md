# KeyBind

A **key binding** allows a certain action to be executed every time the user
presses or releases a certain key. You can register a key binding using a
KeyBind [engine object](../EngineObject).

## Engine Object

You must initialize a key binding engine object with a name, default key and
callback. When the user presses the key bound to a key binding, the callback
will be invoked. The callback can have one of 2 signatures:

1. `bool callback()`
2. `void callback()`

In case of signature 1, returning `false` from the callback will cause the game
to close. Signature 2 behaves the same as 1, except assuming `true` is always
returned.

The key binding will only be valid for as long as its engine object is valid. In
case two key binding objects use the same key, the last one to use the same key
will be invoked. In case two key binding objects use the same name, only the
first one will be invoked. In such cases a warning is emitted to the engine
journal.

The `nwge::KeyBind` class is defined in the `<nwge/bind.hpp>` header. KeyBind
engine objects use object IDs with type `5`.

## Console Commands

You can view a list of key bindings, use the `e.bindList` command:

```console
> e.bindList
Key bindings:
  fwd: w
  bwd: s
  left: a
  right: d
  jump: space
  pause: escape
```

To view a key binding, use `e.bind <key binding name>`:

```console
> e.bind fwd
Key binding `fwd` is bound to `w`
```

You can also check what key is bound to a key with `e.bind <key name>`:

```console
> e.bind w
Key `w` is bound to `fwd`
```

To change a key binding, use `e.bind <key binding name> <key name>`:

```console
> e.bind fwd w
Key binding `fwd` bound to `w`
```

## See Also

* [Event](Event)
