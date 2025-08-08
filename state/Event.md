# Event

The engine will receive **event**s from the operating system and either handle
them internally (such as window resizing) and pass them onto the game
[state](State) (such as mouse clicks and other input).

## Event Types

This is a simple explainer for the event types that will be passed on to your
game's [state](State) and [sub-state](SubState)s. The `nwge::Event` structure is
defined in the `<nwge/event.hpp>` header.

### `MouseMotion`

Emitted when the user moves the mouse cursor within the game window. Data for
this event is stored in the `motion` field of the `Event` structure. The
`Motion` structure contains the following fields:

* `from` is a 2-component vector representing the mouse position before the
  movement occurred. This is in window-space: `(0,0)` is the top left corner of
  the window and `(1,1)` is the bottom right corner of the window.
* `to` is a 2-component vector representing the mouse position after the
  movement occurred. This is in window-space.
* `delta` is a 2-component vector representing the difference between `from` and
  `to`. `(1,0)` means right, `(0,1)` means down.

### `MouseDown` & `MouseUp`

Emitted when the user presses down a mouse button (`MouseDown`) and when the
user releases a mouse button (`MouseUp`). Data for these events is stored in the
`click` field of the `Event` structure. The `Click` structure contains the
following fields:

* `button` is an enumeration representing which mouse button was
  pressed/released. The `MouseButton` enum defined `Left`, `Right` and `Middle`
  for the appropriate mouse buttons.
* `pos` is a 2-component vector representing where the click occurred. This is
  in window-space: `(0,0)` is the top-left corner of the window, `(1,1)` is the
  bottom right corner of the window.

### `MouseScroll`

Emitted when the user scrolls with the scroll wheel of the mouse. Data for this
event is stored in the `scroll` field of the `Event` structure. The data is a
32-bit signed integer. A positive value is scrolling downwards (alike
window-space positioning) and a negative value is scrolling upwards.

### `PostLoad`

Emitted after the engine finishes loading data in the background. Assuming
default engine configuration, of data is enqueued to be loaded outside the main
state's [`preload()` method](State#bool-preload), it will be loaded in the
background. Once all data enqueued in the background is loaded, successfully or
not, this event will be emitted.

## See Also

* [KeyBind](KeyBind)
