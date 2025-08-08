# SubState

A **sub-state** is a smaller state that runs over top of the main
[state](State). Sub-states are stored on the internal sub-state stack. You can
have multiple sub-states active at the same time. The order of the sub-states on
the stack defines the order in which they are updated and rendered. The
sub-state stack also defines some additional options for a given sub-state.

## Options

The following options define how the sub-state will behave within the sub-state
stack:

* `tickParent`: defines if the sub-state above this state in the stack (or the
  main state if this sub-state is at the bottom of stack) should be updated
  every frame.
* `renderParent`: alike `tickParent` defines if the parent should be rendered on
  every frame.
* `receiveEvents`: whether this sub-state's `on()` method should be called.
* `propagateEvents`: whether events handled by this sub-state should also be
  sent to states below this one on the stack.

For example, if you wish to use a sub-state for a pause screen, you can
configure it as follows:

* `tickParent = false`: stop the main state from being updated while paused,
  effectively freezing it in place.
* `renderParent = true`, so we can render the pause screen overtop of the main
  state. In case we don't want the main state to be rendered behind the pause
  screen, this can be `false`.
* `receiveEvents = true`, so the user can interact with the pause screen.
* `propagateEvents = false`, so that the user cannot interact with the main
  state "behind" the pause screen.

Another example: for a dialog box sub-state:

* `tickParent = true`: keep the game running behind the dialog. Depending on
  your preference, you may prefer the game to be paused for more important
  dialog boxes.
* `renderParent = true`: render the game behind the dialog box itself. You may
  also want the dialog box to take up the entire screen, in which case this can
  be set to `false`.
* `receiveEvents = true`: allow the user to interact with the dialog box.
* `propagateEvents = false`: prevent the user from interacting with the main
  state "behind" the dialog box.

These options are defined by the `nwge::SubState::Options` structure defined in
the `<nwge/state.hpp>` header.

## Lifetime

The lifetime of a sub-state is simpler than a regular state.

### Constructor

Unlike regular states, sub-states do not have a `preload()` or an `init()`
method. You should pass in all the assets needed by the sub-state in its
constructor. Sub-states should only be created once your main state has fully
initialized.

Alike regular states, you can pass in a StringView to be used as a label for the
sub-state. The label can be seen in the State Options window of the Debug
Toolbar and in graphics debuggers such as [RenderDoc].

### `bool on(nwge::Event&)`

Called to handle events. This method is called for each sub-state that has
`receiveEvents` set to `true`. It is called in forward order, meaning that it is
called for the state on the top of the stack first and then going down the stack
and eventually at the main state. If any sub-state on the stack has
`propagateEvents` set to `false`, then it'll be the last state to receive the
event. If this method returns `false`, the event is not sent to states below
this one in the stack and the game will close.

### `bool tick(f32)`

Called to run the sub-state logic. This method is called in forward order. In
case this method returns `false`, no states below this one in the stack will be
updated and the game will close.

The `delta` parameter passed to this method is a 32-bit float representing the
amount of time that has passed since the last frame in seconds.

### `void render() const`

Draw the sub-state to the screen. This method is called in reverse order: the
main state is rendered first and then all sub-states starting with the bottom
and going up the stack.

## Stack

Sub-states are internally stored on a stack. The behavior of states given their
options are explained above. Whenever you want to make any change to the stack,
that change is added to an operation queue. Operations from this queue are
executed once the current frame has fully finished. Popping a sub-state from the
stack or pushing a sub-state onto the stack will not update the stack until the
next frame. This is done to ensure that sub-states are only destroyed once they
are no longer present on the sub-state stack.

When the main state changes, the sub-state stack is immediately cleared.

## API

All sub-states must inherit from the `nwge::SubState` class, defined in the
`<nwge/state.hpp>` header. All sub-states must be allocated by nwge's allocator.
When you pass a SubState pointer to the engine, the engine inherently takes
ownership of the sub-state.

You can enqueue a sub-state to be pushed onto the stack with the
`nwge::pushSubState` and `nwge::pushSubStatePtr` functions.

You can enqueue the topmost sub-state to be swapped with a different one using
the `nwge::swapSubState` and `nwge::swapSubStatePtr` functions. In case the
stack is empty, these function is equivalent to `nwge::pushState` and
`nwge::pushSubStatePtr` respectively.

You can enqueue the topmost sub-state to be popped off the stack with the
`nwge::popSubState` function. In case the stack is empty, this will emit a
warning dialog box and the engine will continue running normally.

You can enqueue the sub-state stack to be cleared with the
`nwge::clearSubStates` function.

The above functions are defined in the `<nwge/state.hpp>` header as well.

## See Also

* [State](State)

[RenderDoc]: https://renderdoc.org/
