# State

Every nwge game is made of **State**s -- effectively being a state machine.
Although you can create as many State objects as you wish, only one of them can
be the current state of the game. This state is referred to as the **main
state**.

**Sub-states** are smaller, less complicated states that run on top of the main
state. *TODO: Separate page for Sub-States*

## Lifetime

The methods of a State object are called under specific circumstances and in a
given order.

### Constructor

The `State()` constructor is called when you create the state. It is not safe to
interact with engine APIs in the constructor, especially if the state can be the
initial state passed to the `nwge::startPtr` function.

The `State()` constructor also accepts a StringView that represents a label for
the State. This is used in the State Options window of the Debug Toolbar and
displayed in graphics debuggers, such as [RenderDoc].

### `bool preload()`

The `preload()` method is called by the engine with the purpose of enqueuing
assets to be loaded from [bundle](../data/Bundle)s and/or
[store](../data/Store)s.

Under default engine configuration, when something is enqueued to be loaded in
the `preload()` method, a loading screen will be shown during the loading
process. In case a load like this fails, the game will be closed.

This method should return `true` on success. If `false` is returned, the game
will close.

### `bool init()`

Once all assets enqueued in the `preload()` method are loaded, the `init()`
method is called. The `init()` method is only ever called **after** `preload()`
and all the assets enqueued in it are loaded successfully.

This method should return `true` on success. If `false` is returned, the game
will close.

After this method is called, the sub-state stack is cleared (given that it did
not return `false`).

### `bool on(nwge::Event&)`

The `on()` method is called to handle [event](Event)s in the state. This
includes events such as mouse movements, mouse clicks, background loads
finishing and others. It is recommended to use key bindings to handle keyboard
input.

This method should return `true` on success. If `false` is returned, the game
will close.

### `bool tick(f32)`

The `tick()` method is where you'll run all your game logic. You'll be passed a
delta time to advance the state by the given amount of time.

`tick()` is called twice per frame. The engine will call `tick()` with half the
time passed since the last frame, then call `on()` for each event that occurred
since the last frame, then call `tick()` one more time.

The `delta` parameter passed to this method is a 32-bit float representing the
amount of time that has passed since the last frame in seconds.

This method should return `true` on success. If `false` is returned, the game
will close.

### `void render() const`

The `render()` method is the part where you put things on the screen. This
method is only ever called **after** the second call to `tick()`. You should
avoid modifying game state in this method (which is why it's const-qualified).

If Wireframe is enabled, this method will be called a second time to render the
aforementioned wireframe overtop of the actual rendered graphics.

Most of the engine rendering functionality is disabled outside of this method.

### Abridged

The lifetime of a State boils down to:

* `preload()` once
* `init()` once
* Every frame:
    * `tick()`
    * `on()` potentially multiple times in a row
    * `tick()`
    * `render()`
    * `render()` a second time if wireframe is enabled

## API

All states must inherit from the `nwge::State` class, defined in the
`<nwge/state.hpp>` header. All states must be allocated by nwge's allocator.
When you pass a State pointer to the engine, the engine inherently takes
ownership of the state.

You can enqueue a state change using the `nwge::swapState` and
`nwge::swapStatePtr` functions from the same header.

## See Also

* [SubState](SubState)

[RenderDoc]: https://renderdoc.org/
