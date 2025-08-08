# ConVar

A **console variable** (shortened to ConVar) is a console command that allows
the user to view and change a certain value. These are analogous to ConVars from
the Quake engine and its descendants (such as GoldSrc or Source). ConVars are
implemented as a wrapper around a [console command](Command).

## Usage

The `nwge::console::Var` class template is defined in the
`<nwge/console/Var.hpp>` header. The Var object contains a Command engine object
with a callback defined by the Var class. The command is registered under the
same name as the Var. The command can then be used to view and change the value
of the Var. For example, given a Var named `coolness` of type `s32`:

```console
> coolness
coolness is 0
> coolness 100
coolness is now 100
> coolness
coolness is 100
```

Each Var defines the datatype is stores. Currently (v0.21), only integral and
floating-point numeric types are supported.

You can also define a callback to be invoked when the value of the variable is
set:

```c++
bool init() {
  mCoolnessVar.onSet([](u32 value){
    // value is the new value of the `coolness` console variable
  });
}
```

You can manually change the value of the variable:

```c++
bool tick(f32 delta) {
  // increment `coolness` console variable by 1
  mCoolnessVar.set(mCoolnessVar.get() + 1);
}
```

## See Also

* [Command](Command)
* [Console variable][vdc-console-variable] on the Valve Developer Community

[vdc-console-variable]: https://developer.valvesoftware.com/wiki/Console_variable
