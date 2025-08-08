# Texture

A render system **Texture** is used to apply an image to a mesh during
rendering. A texture [engine object](../EngineObject) is used
to store and manage a singular texture.

Nwge uses OpenGL for its rendering backend, consider giving the Texture article
on the [OpenGL wiki][gl-wiki-texture] a read.

Many of nwge's built-in rendering functions accept a reference to a texture that
should be used during rendering. In case no texture is provided to such
functions, a built-in blank texture is used instead.

When doing rendering via VertexArray and ShaderProgram, you must call the
Texture's `bind()` method to use it for subsequent rendering operations.
Additionally, you must handle texture coordinates in the shaders. The built-in
rendering functions simply accept a texture and texture coordinate parameter and
handle this for you.

Example:

```cpp
void render() const override {
  // bind the texture we want to use for drawing
  mTexture.bind();
  // ALWAYS call use() before drawing to ensure uniforms are updated
  mShaderProgram.use();
  // then issue the draw call.
  mVAO.draw();
}
```

## Engine Object

When a texture engine object is first created, it has no corresponding OpenGL
texture stored on the GPU. If you try to use such object during rendering, a
built-in "Missing Texture" texture will be used instead. Generally, you'll be
loading textures from a [bundle](../data/Bundle) using its `nqTexture` method.
Alternatively, you can load a texture from a file directly with the `load()`
method or upload an [Image](Image) with the `replace()` method.

Note that loading textures uses Image objects internally. Any image you can load
into an Image object can also be uploaded to a Texture.

The `nwge::render::Texture` class is defined in the `<nwge/render/texture.hpp>`
header. Texture engine objects use object IDs with type `7`.

[gl-wiki-texture]: https://www.khronos.org/opengl/wiki/Texture
