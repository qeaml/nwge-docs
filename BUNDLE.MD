# Bundle Files

A bundle file is used to store multiple files inside of one file.

This documents should answer most of your questions about them.

## Why?

Having all needed files be inside one file allows us to load the entire bundle
file into memory and avoid ever interacting with the disk or filesystem while
searching for and reading files.

Another benefit of loading the entire bundle file into memory is the ability to
pre-parse/pre-load specific types of assets instead of storing their raw data.
While this increases the time needed to load a bundle, it reduces load times for
these individual assets. This is most useful when the same texture is loaded
from a bundle multiple times -- the engine doesn't re-parse the data but simply
reuse a preloaded texture.

## Do I need them?

They're not strictly necessary, but preferred.

## Are there any limitations?

Yes:

* Bundle files break completely at ~4GB of data.
* Filenames are stored as a filename + extension combo, where the filename is
  limited to 12 characters and the extension is 4 characters. The filenames are
  also case-insensitive, being stored in all uppercase.

## How do I use them in-engine?

First, include the `<nwge/data/bndl.hpp>` header. Then, you need to decide how
the engine will find your bundle. There are two ways to do this:

1. Use a bundle name and bundle lookup directories.

    Here, you will use the `data::Bundle` constructor accepting a name. When you
    try to load data from such a bundle, the engine will look through the bundle
    lookup directories you specified to find a bundle with that name. This is
    preferred when you know the name of the bundle and compile-time.

2. Use a path directly.

    Use the default `data::Bundle` constructor instead. Before `nq`ing any data,
    you must use the `load()` method of the bundle to specify its path. This is
    preferred when you don't know the bundle's name until runtime. When loading
    a mod, for example.

## How do I make/view bundle files?

Use the `nwgebndl` tool to make and manage bundle files. You can:

* Create a bundle:

    ```console
    $ nwgebndl create <input directory> <output bundle>
    ```

    Note that bundle files _do not support directories_. If there is a
    subdirectory within your input directory, it will be ignored.

* List bundle contents:

    ```console
    $ nwgebndl list <bundle>
    ```

* List bundle contents as CSV:

    ```console
    $ nwgebndl list <bundle> -csv
    ```

* Extract all bundle contents:

  ```console
  $ nwgebndl extract <input bundle> <output directory>
  ```

* Extract specific files from bundle:

  ```console
  $ nwgebndl extract <input bundle> <output directory> REGEX\.PATTERN
  ```
