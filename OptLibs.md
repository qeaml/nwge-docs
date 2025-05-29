# Optional Libraries

Nwge supports a selection of optional libraries to extend nwge's functionality.
Most of these libraries simply allow you to use many more formats for image and
audio data.

You can make sure a library is available to nwge during runtime by bundling it
with you app. You can:

* Place the library files (dll/so) in the same folder as the executable.
* Place the library files in the `libs` folder. (not recommended for all)
* Pray that the user has the library installed system-wide (not reliable!).

For example, to make sure that FreeImage is available, you may simply create a
new folder called `libs` in the folder where your app's executable lives and
place `FreeImage.dll`/`libFreeImage.so` in it. Nwge will automatically find
FreeImage in that folder and load it.

Some libraries aren't fully self-contained and need to load other libraries to
work (`libvorbisfile`, used to load Ogg files, needs to load `libogg` and
`libvorbis` for example). In this case, the most portable solution is to place
the library files in the same folder as the executable. This is due to the way
that the dynamic linker on Linux searches for these libraries. Note that placing
the libraries in the `libs` folder works fine on Windows. (not much we can do
about it on Linux)

Below is a list of these optional libraries, what they do and where to get them.

## Image files

### FreeImage

[**FreeImage**](FreeImage) is a neat, self-contained library supporting a wide
plethora of image formats. Realistically, this would be all you need -
especially if you don't plan on using animated textures. Note that nwge has
built-in support for PNG and JPEG files.

### `libwebp`

[**`libwebp`**](libwebp), as the name suggests, allows us to load WebP images.
Although FreeImage can load WebP images, it cannot load *animated* WebP images.
If you intend to use animated WebPs, you will need to use `libwebp`.

## Audio files

### `libsndfile`

[**`libsndfile`**](libsndfile) is a popular, self-contained library supporting a
wide plethora of audio formats. This is most likely everything you'll need, but
nwge does support certain libraries supporting individual formats in case you
wish to cut down on disk space usage and know exactly which formats you want to
use. Note that nwge has built-in support for MP3 and WAV files.

### `libvorbisfile`

[**`libvorbisfile`**](vorbis) allows us to load Ogg files encoded using
the Vorbis codec. While `libsndfile` can load such files perfectly, you may
prefer to use this library directly if you don't care for the other formats
`libsndfile` provides support for.

### `libFLAC`

[**`libFLAC`**][flac], as the name suggests, allows us to load FLAC files as
well as Ogg files using the FLAC codec. `libsndfile` can load these files
equally as well.

## Debugging

### RenderDoc

If [**RenderDoc**][RenderDoc] is installed on your computer, nwge will use its
API to integrate better with it. Currently there isn't much in the wake of
actual extra functionality added by this integration, but there are some
possibilities to ease graphics debugging.

[FreeImage]: https://freeimage.sourceforge.io/
[libwebp]: https://chromium.googlesource.com/webm/libwebp
[libsndfile]: https://libsndfile.github.io/libsndfile/
[vorbis]: https://xiph.org/vorbis/
[flac]: https://xiph.org/flac/
[RenderDoc]: https://renderdoc.org/
