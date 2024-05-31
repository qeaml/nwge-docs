# NwgeCFN

**NwgeCFN** is a custom format for storing bitmap fonts, developed specifically
for nwge's text rendering system. It is structured in a specific way that allows
the engine to load it quickly and then render text very fast.

This document serves as an explanation of the nwge text rendering system and
the NwgeCFN format.

## Render text, fast

Nwge uses a simple texture-atlas based approach. By optimizing the layout of the
texture atlas, we minimize the amount of data that needs to be sent to the GPU
for a small speed boost. If you were to look at nwge's font shader, you'd see
the following:

```glsl
/// Additional character data:
///  x -> x position of the character,
///  y -> y position of the character,
///  z -> width of the character,
///  w -> texcoord x position
layout (location = 3) in vec4 in_CharData;
```

Nwge only ever sends four floating-point numbers for every character to be
rendered. The shader than properly scales and positions each character:

```glsl
/*
x = char on-screen x + (vertex pos * char width)
y = char on-screen y + (vertex pos * char height)
z = constant z value
*/
vec4 tpos = vec4(
  in_CharData.x + in_Position.x * in_CharData.z,
  in_CharData.y + in_Position.y * in_Height,
  in_Zpos,
  1.0
);
```

This way, the bulk of the calculations is done in the shader.

There's some additional bells and whistles, like the "destretcher" uniform:

```glsl
uniform float in_Destretch;
```

This is used to prevent the text from stretching weirdly due to your monitor's
aspect ratio.

But the most fun part is the texture coordinate calculation:

```glsl
fr_TexCoord = vec2(
  in_CharData.w +
    (in_TexCoord.x * in_CharData.z) /
    (95.0 * in_Destretch * in_MaxWidth * in_Height),
  in_TexCoord.y
);
```

The characters are all stored one after the other, in one line, evenly spaced
apart:

<center>
  <figure>
    <img src="img/CALIBRI.BMP" alt="Font texture atlas">
    <figcaption>
      A snippet of a 11px texture atlas generated from Calibri, scaled 400%
    </figcaption>
  </figure>
</center>

Although this adds extra image data to be stored, it allows us to completely
avoid storing texture coordinates. The U coordinate is calculated from the
character code, while V is always 0.

The font file then stores the width of each character. They may be evenly
spaced, but they may not be evenly sized.

## Compilation

See the [`libnwge_cfn`](libnwge_cfn) and [`nwgecfn`](nwgecfn) documentation.
