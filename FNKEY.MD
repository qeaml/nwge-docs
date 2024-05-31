# Function Keys

Certain function keys (<kbd>F1</kbd> to <kbd>F12</kbd>) are assigned special
engine-level actions.

> [!IMPORTANT]
> All functions keys are reserved for use by the engine. A `KeyBind` cannot be
> bound to a function key.

 Key                            | Action
--------------------------------|-----------------------------------------------
<kbd>F2</kbd>                   | Toggle engine console
<kbd>F11</kbd>                  | Toggle fullscreen mode
<kbd>F12</kbd>                  | Save engine journal to disk
<kbd>Shift</kbd>+<kbd>F12</kbd> | Exit application
<kbd>Ctrl</kbd>+<kbd>F12</kbd>  | Crash application

These keys were added for one of two reasons:

1. To access common functionality quickly (<kbd>F11</kbd>)
2. To be used when the engine console is not available (<kbd>F12</kbd>)

As such, it is generally preferred to use the following console commands
instead:

 Key                            | Equivalent command
--------------------------------|-----------------------------------------------
<kbd>F3</kbd> *(removed)*       | [`e.profiler`](CONSOLE#eprofiler)
<kbd>F6</kbd> *(removed)*       | [`r.wireframe`](CONSOLE#rwireframe)
<kbd>F11</kbd>                  | [`r.fullscreen`](CONSOLE#rfullscreen)
<kbd>F12</kbd>                  | [`e.journal`](CONSOLE#ejournal) ("Save journal to disk" button)
<kbd>Shift</kbd>+<kbd>F12</kbd> | [`e.quit`](CONSOLE#equit)
<kbd>Ctrl</kbd>+<kbd>F12</kbd>  | [`e.testAssert`](CONSOLE#etestassert)
