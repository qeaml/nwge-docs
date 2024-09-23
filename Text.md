# Fonts & Text Rendering

This document describes how fonts & text rendering is handled in nwge.

## Fonts

To understand how text is rendered in Nwge, it is important to understand how
fonts are handled. Nwge does not support TrueType fonts. Instead, it uses its
own NwgeCFN font format. The major difference between NwgeCFN and TrueType
is that NwgeCFN is bitmap-only, whereas TrueType can use both bitmap and
vector rendering. Additionally, the bitmap in a NwgeCFN font is laid out in a
very specific way to allow for fast rendering.

You see, most font texture atlases are laid out in multiple rows. In NwgeCFN,
all characters are laid out in a single row. This saves some memory transfer
between the CPU and GPU, and allows for fast rendering.

All measurement are in pixels.

## Text Rendering

Now that we know what the font texture atlas looks like, we can look at how it
is used to render text. When you render text, nwge will create a glyph data
array. This array contains `vec3`s sent to the font shader for instanced
rendering. Each vector describes the X and Y position of the glyph and its
index. Note that it uses the glyph index, not its character code. That's because
the glyph width information is stored in a uniform array in the shader. Every
time you use a different font, the glyph width array is updated. The shader will
then perform lookups in this array to get the glyph width. Note that if you
always use the same font, the glyph width array will not be updated for best
performance.

Regarding glyph width, it is important to note that two different glyph width
arrays are used. The first array is used to determine the width of the glyph on
the screen. The second array is used to determine the width of the glyph in
the texture atlas. Both of these are calculated from one glyph with array
present in the NwgeCFN font.

Below is an excerpt from the font shader.

```glsl
/*
Glyph instance data:
  X -> glyph X coordinate
  Y -> glyph Y coordinate
  Z -> glyph index
*/
layout (location = 3) in vec3 in_GlyphData;

/* Visible character widths, relative to the glyph height. */
uniform float[94] in_GlyphVisibleWidths;
/* Character width in texture atlas. Should be below or equal to 1/94. */
uniform float[94] in_GlyphTextureWidths;

void main() {
  /* Z component is the glyph index. */
  int glyphIdx = int(in_GlyphData.z);
  /* On-screen width of the glyph. in_DeStretch is used to prevent the glyphs
     from stretching due to aspect ratio. */
  float glyphW = in_GlyphVisibleWidths[glyphIdx] * in_DeStretch;
  /* Actual width and height of the glyph given the height. */
  vec2 size = vec2(glyphW * in_Height, in_Height);
  vec2 glyphPos = in_GlyphData.xy + in_Position.xy * size;

  /* Texture atlas width of the glyph. */
  float glyphTexW = in_GlyphTextureWidths[glyphIdx];
  fr_TexCoord = vec2(
                                /* X coordinate calculated from glyph index. */
    glyphTexW * in_TexCoord.x + in_GlyphData.z / 94.0,
    /* Y coordinate is always 0. */
    in_TexCoord.y
  );
```

## Compilation

See the [`libnwge_cfn`](libnwge_cfn) and [`nwgecfn`](nwgecfn) documentation.
