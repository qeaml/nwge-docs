# libnwge_cfn

This is a small library to aid in reading, writing and generating NwgeCFN files.

## Quick Start

Everything exported from the library is defined in `<nwge/cfn/cfn.h>`.

While working with NwgeCFN, you will need to create a `NwgeCfn` object to store
all the data, for which you'll have to use the `cfnNew()` function:

```c
NwgeCfn font = cfnNew();
```

Then, you can load a font into that object:

```c
SDL_RWops *file = SDL_RWFromFile("font.cfn", "rb");
if(file == NULL) {
  /* handle error */
}
NwgeCfnError err = cfnLoad(&font, file);
if(err != CfnErrorOK) {
  /* handle error */
}
SDL_RWclose(file);
```

What you do with the font from this point onward is entirely up to you.

Once you are done with the font, you must free the allocated memory:

```c
cfnFree(&font);
```

Note that you don't have to de-allocate memory from a font object before loading
another font into it:

```c
void explicit(void) {
  NwgeCfn font = cfnNew();
  SDL_RWops *file = SDL_RWFromFile("font.cfn", "rb");
  if(file == NULL) {
    /* handle error */
  }
  NwgeCfnError err = cfnLoad(&font, file);
  if(err != CfnErrorOK) {
    /* handle error */
  }
  SDL_RWclose(file);

  /* do something with the font */

  cfnFree(&font);
  font = cfnNew();
  file = SDL_RWFromFile("other_font.cfn", "rb");
  if(file == NULL) {
    /* handle error */
  }
  NwgeCfnError err = cfnLoad(&font, file);
  if(err != CfnErrorOK) {
    /* handle error */
  }
  SDL_RWclose(file);

  /* do something with other font */

  cfnFree(&font);
}

void implicit(void) {
  NwgeCfn font = cfnNew();
  SDL_RWops *file = SDL_RWFromFile("font.cfn", "rb");
  if(file == NULL) {
    /* handle error */
  }
  NwgeCfnError err = cfnLoad(&font, file);
  if(err != CfnErrorOK) {
    /* handle error */
  }
  SDL_RWclose(file);

  /* do something with the font */

  file = SDL_RWFromFile("other_font.cfn", "rb");
  if(file == NULL) {
    /* handle error */
  }
  // no need to call Free() - Load() calls it for us
  // no need to call New() - Free() calls it for us
  NwgeCfnError err = cfnLoad(&font, file);
  if(err != CfnErrorOK) {
    /* handle error */
  }
  SDL_RWclose(file);

  /* do something with other font */

  cfnFree(&font);
}
```

Lastly, you may want to generate a NwgeCFN font from a bitmap font. To
effectively replicate what the `nwgecfn` command does:

```c
// 1. Open TrueType font
SDL_RWops *inputFile = SDL_RWFromFile(inputFilename, "rb");
TTF_Font *ttf = TTF_OpenFontRW(inputFile, resolution);
SDL_RWclose(inputFile);

// 2. Generate NwgeCFN font from it
NwgeCfn cfn = cfnNew();
NwgeCfnError error = cfnGenerate(&cfn, ttf);
TTF_CloseFont(ttf);

// 3. Save the NwgeCFN font
SDL_RWops *outputFile = SDL_RWFromFile(outputFilename, "xb");
NwgeCfnError error = cfnSave(&cfn, outputFile);
SDL_RWclose(outputFile);
```

## Atlas data

NwgeCFN stores its texture atlas data as an 8 byte-per-pixel alpha image. This
means that each pixel is just an alpha value. This is implemented using an 8bpp
`SDL_Surface` with a palette that maps each index to the corresponding color
where RGB is white and the alpha value is the index value.
