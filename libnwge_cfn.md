# libnwge_cfn

This is a small library to aid in reading, writing and generating NwgeCFN files.

## API

All of the functions in this library are prefixed with `cfn` and are defined in
`<nwge/cfn/cfn.h>`.

* Create a new, clean NwgeCFN object:
    ```c
    NwgeCfn cfn = cfnNew();
    ```
* Free the memory allocated by the NwgeCFN object. This effectively turns the
  NwgeCFN object into a clean slate.
     ```c
     cfnFree(&cfn);
     // you can now reuse `cfn` for a new font without using `cfnNew()`
     ```
* Loads a NwgeCFN font from an `SDL_RWops` object. This expects the file to be
  in the NwgeCFN binary format, starting with the magic number `NWGECFN`.
    ```c
    SDL_RWops *file; // you must close this file yourself
    NwgeCfnError err = cfnLoad(&cfn, file);
    // check error here...
    ```
* Saves a NwgeCFN font to an `SDL_RWops` object in the NwgeCFN binary format.
    ```c
    SDL_RWops *file; // you must close this file yourself
    NwgeCfnError err = cfnSave(&cfn, file);
    // check error here...
    ```
* Generate a NwgeCFN font from a TrueType font. The `src` parameter is an
  `SDL_RWops` object containing the TrueType font. The `height` parameter is the
  height of the characters in the font. The height of the atlas may be different
  from the size of the characters.
    ```c
    SDL_RWWops *src; // you must close this file yourself
    s32 height; // obtain this from e.g. command-line
    NwgeCfnError err = cfnGenerate(&cfn, src, height);
    // check error here...
    ```
* Check if a font is monospaced. This flag is set by
  `cfnGenerate()`/`cfnLoad()`.
    ```c
    if(CFN_CHECK_MONOSPACE(cfn.flags)) {
      // font is monospaced
    }
    if(cfn.flags & CFN_FLAG_MONOSPACE) {
      // font is monospaced
    }
    ```
* Fonts can use [Run-Length Encoding] to save space. You must set this flag
  before `cfnSave()` to use it. It is automatically set by `cfnLoad()` when it
  loads a font that was saved with RLE.
    ```c
    // to save with RLE
    cfn.flags |= CFN_FLAG_RLE;
    // to check if font was saved with RLE
    if(CFN_CHECK_RLE(cfn.flags)) {
      // font is using RLE
    }
    if(cfn.flags & CFN_FLAG_RLE) {
      // font is using RLE
    }
    ```

## Atlas data

NwgeCFN stores its texture atlas data as an 8 byte-per-pixel alpha image. This
means that each pixel is just an alpha value. This is implemented using an 8bpp
`SDL_Surface` with a palette that maps each index to the corresponding color
where RGB is white and the alpha value is the index value.

[Run-Length Encoding]: https://en.wikipedia.org/wiki/Run-length_encoding
