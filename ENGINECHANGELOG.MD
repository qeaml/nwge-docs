# Nwge Changelog

This document contains changelogs for all publicly available versions of the
engine. Each version is also accompanied by the date on which it was released.

## 0.20

*July 8th, 2025*

* Added `VertexAttribute` helper class.
* Added random number generation functions: `randU64()`, `randInt()`,
  `randF32()`, `randFloat()`.
* Fixed a crash when destroying an invalid object.
* Removed `e.objects` console command.
* Added `e.objectCount` and `e.verboseObjects` console commands.
* Added `e.version` as an alias for the `e.ver` console command.
* Updated the Renderer Options window in the debug toolbar.
  * Added a `Fullscreen` checkbox. This is equivalent to pressing <kbd>F11</kbd>
    or the `r.fullscreen` console command.
  * Added a `Texture Filtering` checkbox that enables or disables filtering for
    Texture and AnimatedTexture objects. This is equivalent to the
    `r.filterTextures` console command.
  * Added a `Font Texture Filtering` checkbox that enables or disables filtering
    for textures used by Font objects.
  * Added a `Disable Textures` checkbox that, when enabled, replaces all
    textures (excluding fonts) with a blank, white texture. This is equivalent
    to the `r.disableTextures` console command.
  * Added a `Cull Mode` list, which is equivalent to the `r.cullMode` console
    command.
  * Updated the object counters to also include the maximum amount of a given
    object.
* Fixed `Font::measure` and `render::measureText`.
* Updated how font account for window aspect ratio.
  * No longer uses an AspectRatio object internally.

## 0.19

*July 5th, 2025*

* Added support for IMA ADPCM encoding in RIFF WAVE files.
* Added support for the [Quite OK Image](https://qoiformat.org) format.
  * Via [this decoder](https://codeberg.org/q/qoi).
* Added support for the [Quite OK Audio](https://qoaformat.org) format.
  * Via the [reference decoder](https://github.com/phoboslab/qoa).
* Added support for non-RGBA textures.
  * If your texture is stored with only 3 color channels (no transparency), then
    the texture will also be stored in that format on the GPU.
  * This will decrease VRAM usage slightly.
* Rewrote a majority of the `render::Image` class.
* Added `r.memory` and `r.fontList`.
* Improved consistency of engine object copying behavior.
  * All engine objects now have proper copy constructors and copy assignment
    operators.
  * Fixed leaks of certain objects when copying.
* Added `render::windowIcon` and `Bundle::nqWindowIcon`.
* Added `render::clearDepth`.

## 0.18

*July 1st, 2025*

* Added `Maybe::orElse`.
* Fixed rare crash when encoding JSON.
* Fixed mods not loading.
* Optimized mod loading.
* Fixed CFN fonts being incorrectly decoded on Windows.

## 0.17

*June 23rd, 2025*

* Improved implementation of certain renderer functions:
  * OpenGL object names are now pregenerated when the renderer is initialized.
    This should make creating and destroying engine-level texture, buffer etc.
    objects faster.
  * `Rect::draw()` (used by `render::rect()`) uses `Primitive::TriangleFan` and
    no longer uses an EBO.
  * Ditto for `render::plane()`.
  * `render::line()` uses a specialized vertex shader and thus no longer
    uses `atan2` or `sqrt`.
* Changed how mouse cursors are handled:
  * Renamed `<nwge/cursor.hpp>` to `<nwge/Cursor.hpp>`.
  * `Cursor` enum was renamed to `CursorState`.
    * `getCursor()` becomes `getCursorState()`, `setCursor()` becomes
      `setCursorState()` etc.
  * Added `CursorState::Custom`.
  * Added `Cursor` object, which represents a custom cursor.
  * Added `NWGE_MAX_CURSOR_OBJECT_COUNT` with a value of 8.
  * Added support for parsing cursors in XCursor format.
* Fixed a potential crash when an invalid object is created before GUI is
  initialized.
* Improved memory layout to reduce amount of small allocations when creating
  certain objects.
* Improved texture upload:
  * If a texture is already in `RGBA32` format, it is no longer converted before
    being uploaded to the GPU.
  * This should improve texture loading performance.
* Fixed mods using case-sensitive names on Linux.
* Improved object pool implementation.
  * The engine is now much less prone to crashing due to uninitialized objects.
* Removed the ability to create duplicate console command objects.
  * Creating two console command objects with the same name is now properly
    detected, in which case only the first object will be valid.
  * A warning will be issued to the engine journal about this.
* Added a limit on how long command names can be (32 characters).
* Rewritten `nwge::String` class.
  * No longer a subclass of `nwge::Array`.
  * Performs optimizations for small strings. In case a string is smaller than
    the size of a pointer, then the string data is stored in place of the
    pointer. This helps eliminate small allocations and slightly improves memory
    usage.
  * Better comments.
  * Optimized `String::formatted()`.
* Added `FauxObject`.
* Fixed `operator ArrayView<const T>()` sometimes causing an ambiguous
  constructor error.
* Added a `data::FileView` constructor accepting a path.
* Fixed `Schema` using non-const `ArrayView`s over array elements.
* Updated config loading code to use file mapping instead of reading the config
  file into memory manually.
* Replaced `json::Value` with a more optimal implementation.
  * More compact memory layout:
    * 3 bits for type
    * 61 bits for an element count (for strings, arrays, objects)
    * either f64 (for numbers) or pointer to data (for string, arrays, objects)
  * This removes a double indirection previously present in the implementation
    (for arrays, `Value` used a pointer to a heap-allocated `Array`, which in
    turn contained its own pointer to the actual heap-allocated data)
  * More consistent handling of copying & moving (moved objects turn into
    `null`)
  * Default constructed values are `null`, and the `nullptr_t` constructor has
    been removed.
* Removed `VertexBuffer::draw`, `render::shape` and `render::put` functions
  accepting a `VertexBuffer`.
* Added `Journal` to `e.memory`.
* Fixed some issues with lengthy journal entries.
  * Entries can now have theoretically infinitely long messages and
    descriptions.
  * Increased the length limit of entries when saving to disk to 1024 (from
    496).
* Added support for `libsndfile`.
  * If `libsndfile` is installed on your system, nwge will be able to use it to
    load many more audio formats.
  * Without `libsndfile` nwge can only load WAVs and MP3s.
  * You can simply bundle a `libsndfile` DLL with your game, nwge will handle
    the rest.
  * If you do not intend to use this extra functionality, it is completely
    optional.
* Added the "Memory Stats" engine GUI window, which shows the same information
  as the `e.memory` console command except in real-time.
* Added `ArrayView` methods:
  * `equals()` and `operator==()`
  * `find()`, `reverseFind()` and `contains()`
* Removed `StringView::equals`, `StringView::find` and `StringView::reverseFind`
  in favor of `ArrayView::equals`, `ArrayView::find` and
  `ArrayView::reverseFind` respectively.
* Fixed bundle tree validation returning errors on valid bundles.
* Fixed 3D camera calculations.
* Optimized bundle tree search.
* Fixed OBJ parser crash when mesh doesn't have texture coordinates.
* Increased camera far Z to 250.
* Added `String` methods:
  * `concat()`, `operator+()`, `operator+=()`
  * `equals()`, `operator==()`
* Added automatic uniform: `in_InvModelMatrix`, the inverse of the model matrix.
* Added support for per-character coloring of text.
  * To take advantage of this, use a `Cursor`.
  * Between adding text to the Cursor (via `operator<<`), simply call
    `render::color()` to change the color of the characters being added.
  * Example:
    ```c++
    auto cursor = myFont.cursor(textAnchor, textHeight);
    render::color();
    cursor << "This text will be white. ";
    render::color({0, 0, 0});
    cursor << "But this will be black.\n";
    cursor.draw();
    ```
* Removed the `KeyDown` and `KeyUp` events.
* Changed how the bundle library and the NwgeCFN library are loaded.
  * These two libraries are now loaded at run-time, as the engine is
    initializing.
  * In case either is missing, the user will be met with a much nicer message
    explaining that a needed library is missing. Otherwise, we rely on the
    operating system relaying that information to the user, which can be quite
    cryptic (see: Linux).
* Added `ArrayView::constBytes()` and removed the const-qualified overload of
  `ArrayView::bytes()`.
* Removed support for SDL2_image.
  * Below are listed some other, optional libraries you can use instead.
  * Nwge will automatically load supported libraries and use them.
  * If none of the optional libraries are found, nwge has built-in fallbacks
    that should provide a good enough set of functionality.
  * This way, you can only ship the libraries you intend on using. For example,
    if you only intend to use PNG & JPEG, then there's no need to use any
    library as nwge has built-in support for the two.
* Added support for FreeImage.
  * This is an optional library. If you bundle it with your app, nwge will
    automatically load it and use it to load `Texture`s.
  * Loading `AnimatedTexture`s is not supported.
  * If not present, `stb_image` is used as a fallback.
  * `stb_image` is only compiled with support for PNG and JPEG.
* Added support for `libwebp`.
  * This is an optional library. If you bundle it with your app nwge will
    automatically load it and use it to load `Texture`s and `AnimatedTexture`s.
  * Unlike FreeImage, `libwebp` can be used to load animated WebPs as
    `AnimatedTexture`s.
* Added support for `libvorbisfile`.
  * This is an optional library. If you bundle it with you app nwge will
    automatically load it and use it to load `Sound`s (and by extension
    `Buffer`s) from Ogg files using the Vorbis codec.
  * Ogg files with other codecs can be loaded with `libsndfile` instead.
* Added support for `libFLAC`.
  * This is an optional library. If you bundle it with you app nwge will
    automatically load it and use it to load `Sound`s (any by extension
    `Buffer`s) from FLAC files and Ogg files using the FLAC codec.
* Added ability to query availability of optional features.
  * In `<nwge/common/feature.h>`, use `nwgeQueryFeature()`.
  * Can be used to throw errors if a library you expect to be present isn't
    available. (In case of a corrupt installation or the user meddling with
    your app's files, for example)
* Removed `Camera::front()` and `Camera::right()`.
* Added `near` and `far` to `CameraSettings`.
* Changed NwgeCFN font generation.
  * Now uses `SDL_ttf` again.
  * Font generation is an optional feature, only available if the `SDL_ttf`
    library is installed on your system.
  * That means the `nwge_cfn` library can be shipped without any reliance on
    `SDL_ttf` being present to function whilst still using its font generation
    capabilities when installed.
* Added `cfnGenerationAvailable()` function to `libnwge_cfn`.
* Added `audio::Buffer` methods:
  * `sampleRate()` and `sampleCount()`
  * Utility `duration()` method that calculates the duration of the buffer's
    audio using the sample rate and sample count.
* Updated CFN format with improved bitmap compression.
  * `cfn.h`: Renamed `CFN_FLAG_RLE` to `CFN_FLAG_COMPRESSED`.
  * `cfn.h`: Removed `CFN_FLAG_RLE2`.
  * `nwgecfn` tool: Renamed `-no-rle` to `-no-compression`.
  * `nwgecfn` tool: Removed `-old-rle`.
* Fixed an issue where uploading new data to a VBO or EBO after it was added to
  a VAO would cause the vertex/element count used for rendering to
  de-synchronize.
* Fixed a crash when loading an OBJ file without normals.
* Added `render::mat::pushTransform()`.

## 0.16

**February 12th, 2025**

* Moved `json::Value` and related definitions to `<nwge/json/Value.hpp>`.
* Added key hashes to `json::Object`.
  * This brings the implementation of `json::Object` and `Table` closer.
  * `json::Object` cannot be `typedef`ed to `Table` since `json::Object` is
    implicitly convertible to `json::Value`, whereas `Table` isn't.
* Fixed engine crashing on loads that shouldn't crash.
* Added `SubState::Options::subTimeScale`.
* Replaced implementation of `Date::today()` and `Time::now()` with an inline
  one using `<chrono>`.
* Added `render::Texture::loaded()`.
* Added `MoveH` and `MoveV` to `nwge::Cursor`.
* Added `render::Cursor::defaultFont()`.
* Added `nwge::KeyBind::keyName()`.
* Fixed app name not being displayed correctly when checking which programs are
  inhibiting screen saver.
  * Some Linux desktops list applications that are preventing the screen from
    being locked (KDE's Power Management applet, for example).
  * This would previously display "My SDL application".
  * It now correctly shows the name of your app.
* Fixed how UTF-8 codepoints are handled in `Path::sanitize()`.
* Added `<nwge/data/FileView.hpp>` header, containing `FileView`.
* Moved `json::parse()` into `<nwge/json/parse.hpp>`.
* Added `json::parse()` overload accepting a `data::RW` to parse files as JSON
  directly.
  * This uses `FileView` internally.
* Added `json::Error` structure, which wraps a `json::ErrorID` providing utility
  operator overloads for more concise code.
* Moved `render::clear()` into `<nwge/render/draw.hpp>`.
* Added State and SubState labels.
  * `State` and `SubState` constructors now accepts an optional StringView to
    use as a label in engine journal and in renderer debug messages.
  * Added `State::label()` and `SubState::label()` to retrieve the
    aforementioned label.
* Removed SubState Stack section from State options temporarily.
* Increased scratch-space allocation history size.
  * You can now make many more scratch-space allocations before it will return
    to the beginning of the memory arena.
* Added support for on-disk shader caching.
  * Requires the `GL_ARB_get_program_binary` extension.
  * Currently, only built-in shader programs are cached.
  * You can view the currently cached shaders in:
    * `$XDG_CACHE_HOME/nwge/shader-cache` (most likely
      `~/.cache/nwge/shader-cache`) on Linux
    * `%APPDATA%\nwge\shader-cache` on Windows
  * Check the engine journal to see the shader cache in action.
* Added `memCopy()` and `memMove()` for `ArrayView`s.
* Added `Path::empty()`.
* Refactored path iteration functionality:
  * Removed `Path::Iterator` and moved it into a separate struct,
    `PathIterator`.
  * `PathIterator` can be constructed directly, `Path::iterate()` is just a
    shorthand for `PathIterator(const Path&)`.
  * `PathIterator` skips over directories and hidden files by default.
  * Added `PathIterator::includeDirs()`, `PathIterator::includeHidden()` and
    `PathIterator::onlyDirs()`.
* Fixed `render::square()` incorrect textures.
* Fixed a crash when calling `Path::ext()` with an empty `StringView`.
* Fixed `SubState`s not being de-allocated if they couldn't be pushed onto the
  sub-state stack.
* Improved RLE format for NwgeCFN fonts. (via `CFN_FLAG_RLE2` flag)
* Added support for automatically-updated uniforms for custom shader programs.
  * For example, if your shader program has a uniform named `in_Tint`, that
    uniform will be updated with the color last set by the `render::color()`
    function so you can tint your shader output correctly.
  * This is handled transparently by the engine.
  * The following uniforms are currently supported:
    * `vec4 in_Tint`: color set by `render::color()`.
    * `mat4 in_Transform`, `mat4 in_ModelMatrix`: obtained from the matrix
      stack, manipulated with functions from the `render::mat` namespace.
    * `mat4 in_ProjMatrix`: projection matrix of the current 3D camera.
    * `mat4 in_ViewMatrix`: view matrix of the current 3D camera.
    * `float in_Time`: time since engine initialization.
* Renamed `Mesh::draw()` to `Mesh::draw3D()`.
* Added `Mesh::draw2D()` and `Mesh::draw()`.
* Moved `Mesh::bind()` to private, as it's only used internally.
* Replaced the scratch-space implementation with `qmlScratch`
  (https://github.com/qeaml/scratch)
  * Additionally, the memory is now stored in a dedicated memory page rather
    than being stored alongside all other engine state. This will make the
    scratch-space more resistant to memory corruption, buffer overruns etc.
* Removed `nwgeScratchQuery()`.
* Updated output of `e.scratch`.
* Fixed memory leaks when changing state.
* Fixed memory leaks when shutting down the engine
* Added `e.memory` console command.
* Removed `e.alloc` and `e.scratch` console commands, being replaced with
  `e.memory`.
* Added `VertexArray::defaultVertexLayout`.
* Changed default `CullMode` to `CounterClockwise`, as per most other 3D
  applications.
  * The engine will no longer reorder triangles in STL files.
* (Linux-only) Added invalid memory access diagnosis.
  * Whenever a SIGSEGV is caught, the engine will examine the address that
    caused it and display a likely cause of the error.
  * For example, there are guard pages set up around the engine scratch-space.
    If one was to write to these guard pages the kernel will generate a
    segmentation fault. The engine can detect that the faulty address is in one
    of the guard pages and thus suggest the error may be a scratch-space buffer
    overrun.
  * Because Windows does not provide `sigaction()`, this is only available on
    Linux. To examine the address of a segmentation fault on Windows, you must
    use a debugger. (at which point you can examine the code to find out what
    went wrong)

## 0.15

*December 22nd, 2024*

* Fixed `nwgebndl extract` file name filter.
* Added `CodepointIterator`.
* Updated string rendering to properly handle UTF-8 codepoints.
* Updated JSON parser & encoder to accept UTF-8 codepoints.
* Updated file path parser to accept UTF-8 codepoints.
* Reintroduced `render::text`.
* Added support for MP3 files.
* Improved handling of engine panic before the renderer has been fully
  initialized.
  * Before: The app would freeze with no way to exit it other than to terminate
    the process.
  * Now: The engine will check if a usable window is present and show a dialog
    box if not.
* Removed a lot of bloat from the engine journal.
* Improved how the sub-state stack is implemented to avoid a ton of potential
  use-after-free issues.
  * Fixed some edge cases with sub-state ordering.
* Added `nwge::clearSubStates()`.
* Improved how data loading queues are defined.
  * Added dedicated queues for `AnimatedTexture`s, `Sound`s and
    `audio::Buffer`s.
  * Removed `JSON` queue.
  * All queues are now defined in the `data/queues.ipp` header.
* Added `render::Mesh`.
* Added STL and OBJ model loading support.
  * **Note:** Nwge, by default, uses clockwise
    winding order. Most other software, on the other hand, uses
    counter-clockwise. When loading an STL file, Nwge will reorder the
    triangles' vertices. Nwge does not reorder vertices in OBJ files.
  * **Tip:** Use the `render::cullMode` and `render::resetCullMode` functions to
    control winding order.
* Removed in-engine profiler.
* Added a call to `glPushDebugGroup()` for main state render and every sub-state
  render. (and an appropriate `glPopDebugGroup()` call of course)
  * This should make it easier to troubleshoot issues with multiple sub-states
    rendering incorrectly.
* Improved error handling in `libnwge_bndl`.
  * Bundle file structure is now validated. Files can no longer overlap the
    header or file tree.
  * Errors are now properly shown in the `nwgebndl` CLI.
  * `bndlFreeWriter` now returns a `BndlErr`. The function has also been marked
    as no-discard to ensure the error is handled after updating.
* Improved implementation details of `nwgebndl` CLI.
* Added basic mod support.
  * Mods are folders inside the `mods` folder in the same directory as the main
    app executable.
  * Each mod contains a folder for each bundle file it modifies.
  * Files placed in a bundle folder will replace the files present in the bundle
    with the ones from the modded folder instead.
  * Example: `mods/.../game/Settings.json` will replace `Settings.json` in
    `game.bndl`.
* Fixed `Path::ext()` incorrectly handling empty extensions.
* Removed custom bundle lookup directories.
  * Bundles are loaded from either the current working directory or from the
    `data` directory.
* Fixed sub state stack ticking inexistent sub states.

## 0.14

*October 14th, 2024*

* Changed how engine GUIs are handled internally. This changes how some
  engine-level key bindings behave:
  * The journal/crash button was changed from <kbd>F12</kbd> to <kbd>F10</kbd>.
    Additionally, it now works the same regardless of whether the debugging
    toolbar is visible or not.
  * Pressing function keys no longer causes key bindings to be triggered.
  * Fixed some minor Dear ImGui API usage issues.
  * Changed debugging toolbar key bindings:
    * <kbd>F2</kbd> - toggle console.
    * <kbd>F3</kbd> - toggle journal viewer.
    * <kbd>F4</kbd> - toggle profiler.
    * <kbd>Esc</kbd> - hide the debugging toolbar.
* Changed `NWGE_UNREACHABLE` & `nwgeUnreachable()` to accept a message.
* Improved console implementation.
* Removed `console::show()`.
* Added `console::Command::run()`.
* Changed some internal implementation details of various engine subsystems,
  which may result in minor performance improvements.
* Fixed a potential buffer overflow in `cliParse()`.
* Added basic support for 3D rendering.
  * Added `render::model`, which simply renders the provided mesh using the
    current matrix stack as the model matrix.
  * Added `render::plane`, which accepts a transform object defining its
    position, rotation and scale as well as an optional texture.
  * Added `render::Camera`.
    * Use `Camera::makeCurrent()` to make the camera the current camera. This
      will cause all future rendering to use the camera's settings, position,
      rotation etc.
    * Use `Camera::settings()` to view or change the camera's settings.
    * Use `Camera::pos()` to get the camera's position.
    * Use `Camera::move()` to move the camera.
    * Use `Camera::rot()` to get or change the camera's rotation.
    * Use `Camera::front()` to get the camera's front vector.
    * Use `Camera::right()` to get the camera's right vector.
* Rewrote mouse cursor management code.
  * Fixed mouse capture.
* Fixed "Quit App" button in the debugging toolbar.
* Added ability to change cull mode on the fly.
  * Added `render::cullMode()`.
  * Added `r.cullMode` command.
* Added `Cursor::Hidden` and `Cursor::Captured`.
* Removed `releaseCursor()`, use `defaultCursor()` instead.
* Fixed window not being resizable.
* Increased maximum amount of Shaders and Shader Programs to 16 each.
* Added audio system.
  * 16-bit signed 44.1 kHz audio.
  * Uses MojoAL for audio playback and dr_wav for loading WAV files.
  * Added `audio::Buffer`.
  * Added `audio::Source`.
  * Added `audio::listenerXxx` functions.
  * Added `gainScale` user configuration option.
  * Added `config::setGainScale` and `config::getGainScale`.
  * Added `a.gain` command.
* Changed CFN generation to use stb_truetype instead of SDL_ttf.
  * All functions which previously accepted a `TTF_Font` pointer now accept an
    `SDL_RWops` for a TrueType font file as well as an atlas height.
  * Monospace fonts are currently not supported and all fonts will be generated
    as a variable-width font.
  * SDL_ttf is no longer a required dependency.
* Added `CfnErrorInvalidArg`, `CfnErrorUnimplemented`.
* Renamed `CFN_FLAG_X_MASK` to `CFN_FLAG_X`.
* Added support for Run-Length Encoding in NwgeCFN fonts.
* Changed the font compiler to use RLE by default.
  * Added `-no-rle` flag to disable RLE.
* Added `Path::Iterator` and `Path::iterate()`.
* Added `Path::isFile()` and `Path::isDir()`.
* Changed built-in blank texture to no longer use up an entry in the texture
  object pool.
* Added `r.textures` to enable & disable textures.
* Added `json::ObjectBuilder` and `json::ArrayBuilder` for easier construction
  of these values.
* Added `json::Schema` for easier parsing and validation of JSON objects and
  arrays.
* Added `std::formatter` for `Maybe`.
* Added `Table`.
* Added `XXH64`.
* Changed `Slice::push()` to return a reference to the newly pushed element.
* Added `Slice::emplace()`.
* Changed how window state is saved to config file:
  * Added `windowState`:
    * `Default` is used when the user didn't change anything about the window.
    * `Resized` is used when the user resized the window.
    * `Maximized` is used when the user maximized the window.
    * `Fullscreen` is used when the user toggled fullscreen mode.
  * Changed `windowW` and `windowH` to only be saved when `windowState` is
    `Resized`.
  * Added `config::WindowState` and `config::User::windowState`.
  * Changed window size to be separate from viewport size and render resolution.
* Added `config::Dev::filterTextures`.
  * Defaults to `false` to replicate old behavior.
* Added `r.filterTextures` command.
* Added console command history.
* Fixed `e.quit` and toolbar's `Quit App` button not working.
* Added the kill screen.
* Fixed the built-in default font.
* Added `Store::nqDelete`.
* Added `rebuild` action to the `nwgecfn` tool.
    ```sh
    nwgecfn rebuild <input NwgeCFN font> [output NwgeCFN font]
    ```
* Rewrote the entire fonts & text rendering code.
  * Changed NwgeCFN version to v2. NwgeCFN v1 is no longer supported.
  * Updated built-in default font.
  * Improved font texture atlas generation.
  * Removed support for loading TrueType fonts.
  * Removed `Font` methods:
    * `save()`
    * `generate()`
    * `createMonospace()`
    * `createVariable()`
* Added support for Nwge Bundle version 2.
  * V2 offers support for file modification times:
    * Currently, this is only supported by the binary format. The API does not
      support this.
  * V2 ensures file tree integrity by checking the file tree hash.
    * This is performed automatically when loading or saving a bundle.
    * This uses the xxHash64 algorithm.
  * V2 supports flags, which allow for more advanced features to be added in a
    backwards-compatible manner.
* Added `d.help` and `d.bundleList` commands.

## 0.13

*August 11th, 2024*

* Added new `StringView` methods:
  * `trimPrefix()` & `trimSuffix()`
  * `find()`
* Rewrote some Linux-specific code (memory usage, debugger detection & distro
  information)
* Added more system information to journal when pre-initializing engine.
* Fixed `nwgebndl list` not displaying 12-character long filenames correctly.
* Added 16-byte alignment to all file data in newly created bundle files. This
  does not invalidate any existing bundles and only affects newly created
  bundles. This affects `libnwge_bndl` directly, and as such no code changes are
  necessary.
* Added warning about long filenames and/or extensions to `nwgebndl create`.
* Added `cliInit()`.
* Added `CLI_ESC`, `CLI_SGR` and multiple `CLI_SGR_XXX` macros.
* Changed `nwgebndl create` to be less noisy.
* Changed engine directories to be more compliant with the XDG Base Directory
  Specification on Linux. The Windows version is unchanged.
* Added `recurse` parameter to `Path::mkdir()` to allow for recursive
  directory creation.
* Added journal dump if the engine fails to pre-initialize or initialize.
* Updated journal file & terminal output formatting.
* Updated data system teardown process.
* Updated console system teardown process.
* Fixed some edge cases where crashing during state changes would cause
  resources to leak.
* Rewrote render system. Major changes include:
  * Renamed `<nwge/render/font.hpp>` to `<nwge/render/Font.hpp>`.
  * Renamed `<nwge/render/aspectRatio.hpp>` to `<nwge/render/AspectRatio.hpp>`.
  * Removed all `<nwge/render/gl/...>` header files.
  * Removed `<nwge/render/gl.hpp>` header.
  * Removed `render::hideWindow()`, `render::showWindow()`, and
    `render::toggleWindow()`.
  * Split `render::setScissorEnabled()` into `render::enableScissor()` and
    `render::disableScissor()`.
  * Removed `r.wireframe` and `r.info`.
  * Added `r.textureList`, `r.shaderList` and `r.programList`.
  * All render objects are now engine objects. The corresponding
    `NWGE_RENDER_MAX_*_OBJECT_COUNT` macros have been added to `engineLimits.h`.
  * Added the following engine object types:
    * `Texture: 7`
    * `Shader: 8`
    * `ShaderProgram: 9`
    * `Buffer: 10`
    * `VertexArray: 11`
  * Removed the `nwge::render::gl::` namespace. All names previously within it
    are now inside `nwge::render` instead.
  * Objects that were previously subclasses of `Buffer` now simply wrap a plain
    `Buffer` object.
  * Shaders are now properly detached from shader programs upon linking,
    allowing for them to be properly destroyed.
* Added `ArrayView::bytes()`.
* Added `CullMode`.
* Updated mouse motion event:
    Instead of a simple `vec2` for the mouse motion, it now contains
    the position from which the movement began, the position where the movement
    ended and the relative movement.
* Improved state system, which should result in a small performance boost. This
  affects the main engine loop as well as event handling & key bindings.
* Improved handling of missing & invalid textures. The engine should now catch
  many more cases of such textures.
* Added `AnimatedTexture`.
* Improved JSON string parsing & encoding.
* Added `r.bufferList`.
* Added `BBox`.
* Added `std::formatter` for `glm::vec3`. (in `render/Vertex.hpp`)
* Removed `render/misc.hpp`.
* Added `render::line()`.
* Changed app state de-init. The states are now destroyed right after the engine
  exits the main loop. Thus, any code that runs after the engine returns from
  `start()`/`startPtr()` will run after all states have been destroyed.

## 0.12

*July 13th, 2024*

* Added `NWGE_UNREACHABLE` and `nwgeUnreachable(...)`
* Moved `console::Callback` into `console::Command`
* Added `console::Var`
* Removed `dialog::input` and `dialog::password` functions and replaced them
  with `dialog::Input` class
* Improved handling of empty title and message for various dialog boxes
* Added `Path::rename` and `data::nqRename`
* Added `FilePicker::defaultPath`
* Added `data::nqSave`
* Added `_MIN`, `_MAX`, `_BITS` macros for `f32`, `f64`, `ssize` and `usize`.
* Added `F32_EPSILON` and `F64_EPSILON`.
* Added implementation of `render::windowTitle`.
* Added `dialog::color()`.
* Added console & journal output to terminal if one is present. (Linux only)
* Added operating system information to journal for debugging purposes and
  `e.ver` console command.
* Added `Date` and `Time`.
* Added `openURL`.

## 0.11

*June 8th, 2024*

* Added open/save file dialogs. (`nwge::dialog::FilePicker`)
* Added folder picker dialogs. (`nwge::dialog::pickDirectory`)
* Added `nwge::console::show()`
* Added 'Tick Once' button to state options.
* 'Break and tick' and 'Break and render' buttons in state options now ask for
  confirmation when a debugger isn't attached.
* Fixed `dialog::confirm()` always returning false.
* Added the following statistics to the profiler:
  * Minimum: The shortest time between when the tick began
    and the render finished.
  * Maximum: Similar to Minimum, but the longest time instead.
  * Average: Similar to Minimum and Maximum, but the average time instead.
* `nwgebndl create` now ignores files ending with a tilde (`~`).
* Changed key binds to a callback-based approach.
* Added `Path` methods: `open()`, `copy()`.
* Added `Store::path()`.
* Added `data::nqCopy()`.
* Added prompt asking user to open the journal file after it is dumped to disk.
* Added `dialog::input()` and `dialog::password()`.
* Added `dialog::Choice`
* Added `render::gl::Texture::load(data::RW&)` to satisfy `Loadable`.
* Added the ability to load files directly from paths via `data::nqLoad(const
  Path&, Loadable&)`
* Added `Event::MouseScroll`
* Added automatic culling to `render::rect` and text renderer.
  * If the engine determines that a rectangle is outside the screen, then the
    command to draw it is never sent to the GPU. Note that if you manually
    manipulate the rectangle's position via the matrix stack, the engine may
    mistakenly decide not to render your rectangle. Please ensure that is not
    the case.
  * If a character from a text string is determined to be outside the screen,
    then that character is discarded. Note that each character in the string is
    considered individually, so only some substrings may be rendered instead of
    the full string.
* Separated `render::gl::ArrayBuffer` (for any type of data) and
  `render::gl::VertexBuffer` (specifically for vertex data)
* Added `render::gl::Stats::vertexBuffers`, counting the amount of
  `VertexBuffer` objects
* Changed how character data buffers are handled by text renderer.
  * Each font now has its own character data buffer.
  * Improves cache locality for renders.
  * Avoids potential conflicts when using `Cursor` objects with multiple fonts.
* Added `toUpper(ArrayView<char>)` and `toLower(ArrayView<char>)`.
* Fixed `isSpace()` considering non-ASCII characters as spaces.
* Added proper copy and move constructors for `Object` and all its subclasses.
* Added `render::square`
* Fixed journal buffer overrun
* Fixed some ordering issues in engine shutdown
* Added fallback directories to place journal files in in case the usual path
  could not be determined.
* Downgraded to OpenGL 3.3, which disables some rendering features
