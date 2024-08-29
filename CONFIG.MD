# Engine Configuration

## Two Types of Config

### Developer-facing Config

These configuration options dictate how the developer expects the engine to act.
They are passed in the `nwge::start` call and cannot be modified afterwards.

* [Load priority] & [Load blocking].
* Application name.
* Window options: Resizable, borderless, aspect ratio.
* Initial cursor state: Captured, which cursor to use.
* Initial user-facing configuration options.

### User-facing Config

These configuration options dictate how the user expects the engine to act. They
are provided default values by the developer and may be changed by the user at
runtime, either via console command or with a configuration file.

* Window size.
* Initial full-screen mode.
* FPS limiter: Automatic or manual.
* Audio gain.

The user may override these options in many ways. Below is a list of steps taken
to determine the final values:

1. Developer-provided defaults.
2. Load overrides from `$NWGE_HOME/config.json`
3. Load overrides from `$NWGE_HOME/config/<app name>.json`

The user is provided with a Configuration Manager in-engine. It then allows the
user to modify either the global config (2) or a per-app config (3). This
Configuration Manager will be available under the `e.config` console command.

## Manipulating the User-facing Config

The `engineConfig.h` header will expose a simple API allowing the app the modify
the user's app config (3 from above). This will allow developers to create
immersive "Options" menus.

The user still has the options of creating their own overrides via the
Configuration Manager.

[load priority]: DATA.MD#load-priority
[load blocking]: DATA.MD#blocking-loads
