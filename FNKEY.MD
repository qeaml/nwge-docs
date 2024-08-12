# Function Keys

Certain function keys (<kbd>F1</kbd> to <kbd>F12</kbd>) are assigned special
engine-level actions.

> All functions keys are reserved for use by the engine. A `KeyBind` cannot be
> bound to a function key.

 Key                            | Action
--------------------------------|-----------------------------------------------
<kbd>F2</kbd>                   | Toggle debugging toolbar
<kbd>F10</kbd>                  | Save engine journal to disk
<kbd>Shift</kbd>+<kbd>F10</kbd> | Exit application
<kbd>Ctrl</kbd>+<kbd>F10</kbd>  | Crash application
<kbd>F11</kbd>                  | Toggle fullscreen mode

These keys were added for one of two reasons:

1. To access common functionality quickly (<kbd>F11</kbd>)
2. To be used when the engine console is not available (<kbd>F12</kbd>)

As such, it is generally preferred to use the following console commands
instead:

 Key                            | Equivalent command
--------------------------------|-----------------------------------------------
<kbd>F10</kbd>                  | [`e.journal`](CONSOLE#ejournal) ("Save journal to disk" button)
<kbd>Shift</kbd>+<kbd>F10</kbd> | [`e.quit`](CONSOLE#equit)
<kbd>Ctrl</kbd>+<kbd>F10</kbd>  | [`e.testAssert`](CONSOLE#etestassert)
<kbd>F11</kbd>                  | [`r.fullscreen`](CONSOLE#rfullscreen)

If the debugging toolbar is visible, then the following keys are also
available:

 Key                            | Action
--------------------------------|-----------------------------------------------
<kbd>F1</kbd>                   | Open nwge documentation
<kbd>F2</kbd>                   | Toggle console
<kbd>F3</kbd>                   | Toggle journal viewer
<kbd>F4</kbd>                   | Toggle profiler
<kbd>F9</kbd>                   | Hide debugging toolbar

All other key binds still work when the debugging toolbar is visible. The above
keys are only available when the toolbar is visible.
