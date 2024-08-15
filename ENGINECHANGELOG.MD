# Nwge Changelog

This document contains changelogs for all publicly available versions of the
engine. Each version is also accompanied by the date on which it was released.

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
