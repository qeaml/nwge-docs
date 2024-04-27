# Engine Initialization

Nwge is initialized in 3 distinct stages:

1. Pre-initialization
2. The `main` function
3. Initialization

## Pre-initialization

The first stage is **pre-initialization**, which takes place as soon as your app
starts, but before your `main` is called. This is achieved by the
`<nwge/main.hpp>` header, which provides its own `main` function and silently
renames your main function to `nwgeAppMain`. This `main` function provided by
nwge simply calls the `nwge::launch` function with a pointer to your `main`
function. Pre-initialization is performed in `nwge::launch`. In case
pre-initialization fails, your `main` is *never called*. That way, you can
assume most of nwge's systems are already initialized and ready to go by the
time execution reaches your `main` function.

Pre-initialization consists of the following:

1. Register signal handlers
2. Pre-initialize console (set up internal pointer tables)
3. Calculate engine paths
4. Initialize SDL (and set up internal system cursor pointer table)
5. Initialize renderer (with invisible window)
6. Initialize engine GUIs
7. Post-initialize console (print help message)

Note that at this point, we don't know the app name yet. This means we cannot
load a user configuration file for the app, so a path is not calculated for it.

If all of the above steps complete successfully, execution is then handed off to
your `main` function.

## The `main` function

You likely have a simple, bare bones `main` that looks something like this:

```cpp
s32 main([[maybe_unused]] s32 argc, [[maybe_unused]] CStr *argv) {
  nwge::startPtr(createState(), {
    .appName = "My Nwge App",
  });
  return 0;
}
```

Such a `main` function simply passes execution back to nwge without doing
anything extra. Having such a `main` function is useful, however, if you wish to
parse command-line arguments (using `libnwge_cli`, for example) before starting
your app. This also means you can early out in case something goes wrong during
that process.

## Initialization

The rest of the initialization is performed from your call to `startPtr`
(`start` is just a wrapper over `startPtr`). This is where initialization that
needs the [developer configuration][dev-cfg] is performed.

Pre-initialization consists of the following:

1. Initialize configuration manager
2. Update previously calculated paths (e.g. config path as now we have app name
   for it)
3. Load user configuration
4. Renderer post-initialization tasks (e.g. make the window visible)
5. Preload state
6. Enter main engine loop

At this point nwge retakes execution and finishes everything that wasn't fully
initialized. For example, the user's app config is loaded here, since we didn't
know the app name before (the config file is named after the app). We also
re-initialize any [Store][store] paths.

[dev-cfg]: CONFIG#developer-facing-config
[store]: DATA#data-stores
