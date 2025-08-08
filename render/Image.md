# Image

An **Image** object is used to represent a rectangular area of pixels stored in
RAM and accessible by the CPU. Images are a CPU equivalent to GPU
[Texture](Texture)s.

Images can have any number of channels, though textures only support RGB (3
channels) and RGBA (4 channels).

By default, images can be loaded from PNG, JPEG and QOI files. If `libwebp` is
present, WebP files can be loaded too.

The `nwge::render::Image` class and the utility `nwge::render::RGB` and
`nwge::render::RGBA` structures are defined in the `<nwge/render/Image.hpp>`
header.

## See Also

* [Texture](Texture)
