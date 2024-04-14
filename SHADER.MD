# Nwge & Shaders

This document will explain the built-in shaders used by nwge, how you can
utilize them, as well as tips on writing your own shaders.

## Table of Contents

* [Nwge \& Shaders](#nwge--shaders)
  * [Table of Contents](#table-of-contents)
  * [Built-in](#built-in)
    * [Available to App](#available-to-app)
      * [`Coord`](#coord)
      * [`Fragment`](#fragment)
    * [Internal](#internal)
      * [`Shape`](#shape)
      * [`Font`](#font)
    * [Get yourself some](#get-yourself-some)
  * [Custom](#custom)
  * [Built-in source code](#built-in-source-code)
    * [`Coord` source code](#coord-source-code)
    * [`Fragment` source code](#fragment-source-code)
    * [`Shape` source code](#shape-source-code)

## Built-in

Nwge has a total of 4 built-in shaders. Only 2 of them are available to the app,
however.

### Available to App

Nwge provides 2 shaders you can freely utilize.

#### `Coord`

The `coord` shader is the default vertex shader used by nwge when you don't
provide your own. It's main purpose is to translate coordinates you use in
rendering from the more intuitive nwge coordinates to coordinates OpenGL is
happy with.

Think of this as follow:

* From: (0, 0) is top left and (1, 1) is bottom right
* To: (-0.5, -0.5) is top left and (0.5, 0.5) is bottom right.

#### `Fragment`

The creatively named `fragment` shader is the default fragment shader used by
nwge when you don't provide your own. It performs texturing as well as applying
a customizable tint.

The tint is customized using the `in_Tint` uniform, which is defined as follows:

```glsl
uniform vec4 in_Tint;
```

### Internal

Nwge also utilizes 2 shaders which are completely hidden from your app.

#### `Shape`

The `shape` shader is the vertex shader used to implement 2D shape rendering. It
includes transformation via the engine's matrix stack, texture scale & offset as
well as tinting via the engine's `render::color()` function.

This shader is used through the `render::put()` function, which in turn is
invoked by `render::rect()`, for example.

Since this shader is mostly meant for 2D rendering, it doesn't do anything in
the direction of projection or anything. Since you do have access to the engine
matrix stack, you can simply calculate your own projection matrix and still use
this shader to render 3D geometry. Nwge doesn't provide any 3D rendering
primitives out-of-the-box, but it is completely possible to render 3D rendering
with some extra tinkering.

#### `Font`

The `font` shader is the vertex shader used to implement instanced text
rendering. If you want to know more about how nwge's text rendering works, see
the [Font Renderer docs](CFN#render-text-fast).

To break it down simply, this shader accepts some highly compressed character
data and properly positions it on screen as well as calculating the correct
texture coordinates.

### Get yourself some

If you want to get a reference to the built-in shaders, use the following
methods:

```cpp
namespace nwge::render::gl {
struct Shader {
  // ...

  static const Shader &defaultVertex(); // <- `coord` shader

  static const Shader &defaultFragment(); // <- `fragment` shader

  // ...
} // struct Shader
} // namespace nwge::render::gl
```

## Custom

You can also write your own shaders, if you need anything more than the bare
bones shaders that nwge provides. While there is no easy way to load shaders
from bundles (currently, April 2024), it is still possible to use your own
shaders via the `Shader` and `ShaderProgram` structures in the
`nwge::render::gl` namespace (see `<nwge/render/gl.hpp>`).

Keep in mind: when implementing vertex shaders you have to perform the
nwge-to-OpenGL coordinate conversion yourself, unless you wish to use the less
intuitive coordinate space within your app.

To make it easier for you to implement your own shader, the source code of the 2
built-in shaders and the `shape` shader is provided below, licensed under the
BSD 3-Clause License (like the rest of this documentation).

## Built-in source code

### `Coord` source code

```glsl
#version 330 core

/*
coord.vert.glsl
--------------------
Generic vertex shader used as a default when no vertex shader is provided.
*/

layout (location = 0) in vec3 in_Position;
layout (location = 1) in vec2 in_TexCoord;
layout (location = 2) in vec3 in_Color;

out vec4 fr_Color;
out vec2 fr_TexCoord;

void main() {
  gl_Position = vec4(2.0*in_Position.x-1.0, -2.0*(in_Position.y)-1.0, in_Position.z, 1.0);
  fr_TexCoord = in_TexCoord;
  fr_Color = vec4(in_Color, 1.0);
}
```

### `Fragment` source code

```glsl
#version 330 core

/*
fragment.frag.glsl
--------------------
Basic fragment shader, used both within the engine and as a default when no
fragment shader is provided.
*/

out vec4 FragColor;

in vec2 fr_TexCoord;
in vec4 fr_Color;

uniform sampler2D in_Texture;
uniform vec4 in_Tint;

void main() {
  FragColor = texture(in_Texture, fr_TexCoord) * fr_Color * in_Tint;
}
```

### `Shape` source code

```glsl
#version 330 core

/*
shape.vert.glsl
--------------------
The shape shader, used to render 2D geometry.
*/

layout (location = 0) in vec3 in_Position;
layout (location = 1) in vec2 in_TexCoord;
layout (location = 2) in vec3 in_Color;

uniform mat4 in_Transform;
uniform vec2 in_TexPos;
uniform vec2 in_TexSz;

out vec4 fr_Color;
out vec2 fr_TexCoord;

void main() {
  // 1. Translate coordinates using matrix obtained from engine matrix stack
  vec4 tpos = in_Transform * vec4(in_Position, 1.0);
  // 2. Translate to normalized device coordinates
  gl_Position = vec4(2.0*tpos.x-1.0, 2.0*(1.0-tpos.y)-1.0, tpos.z, 1.0);
  // 3. Scale & offset texture coordinates
  fr_TexCoord = in_TexCoord * in_TexSz + in_TexPos;
  // 4. Pass vertex color along to the fragment shader. The fragment shader
  //    applies the tint.
  fr_Color = vec4(in_Color, 1.0);
}
```
