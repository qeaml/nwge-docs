# Nwge SDK

The nwge SDK is the package of binaries, import libraries (on Windows) and
header used to develop apps with the nwge engine.

The SDK is currently not publicly released.

## Structure of an SDK

When opening a nwge SDK tarball, you'll be greeted with the following file
structure:

* `bin` directory, containing tool binaries
* `include` directory, containing all of nwge's headers
* `lib` directory, containing shared objects on Linux or DLLs and import
  libraries on Windows
* `install.sh` or `install.bat` scripts, which automatically install the SDK on
  your system, see below
* `version` file, containing the nwge SDK version

These files are intended to be redistributed with your apps.

## The tools

The SDK provides the following tools:

* `nwgebndl`, used for creating, viewing and extracting bundle files. See the
  [data system documentation](DATA) for more information.
* [`nwgecfn`](nwgecfn), used for managing NwgeCFN fonts.

## The libraries

The SDK provides the following libraries:

* `libnwge.so`/`nwge.dll`, the core engine.
* `libnwge_bndl.so`/`nwge_bndl.dll`, the bundle library.
* [`libnwge_cfn.so`/`nwge_cfn.dll`](libnwge_cfn), the NwgeCFN library.
* `libnwge_cli.so`/`nwge_cli.dll`, the CLI parser.

## The install script

The SDK provides an install script for both platforms with installs the SDK on
your system, thus requiring to be run as administrator/root.

On Windows, the `install.bat` script:

* Copies nwge binaries & headers into `%ProgramFiles%\Nwge SDK`.
* Creates links to nwge DLLs & tools in `%SystemRoot%`. This is done to avoid
  adding any new directories to your PATH.
* Detects Visual Studio and creates links to nwge import libraries & headers for
  your Visual Studio install.

On Linux, the `install.sh` script:

* Copies nwge headers into `/usr/include`.
* Copies nwge tools into `/usr/bin`.
* Copies nwge shader objects into `/usr/lib` and creates versioned symlinks to
  them.
