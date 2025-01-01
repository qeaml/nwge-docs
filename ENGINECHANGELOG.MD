# Nwge Changelog

This document contains changelogs for all publicly available versions of the
engine. Each version is also accompanied by the date on which it was released.

## 0.15

**December 22nd, 2024**

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

**October 14th, 2024**

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

**August 11th, 2024**

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

**July 13th, 2024**

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

**June 8th, 2024**

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
