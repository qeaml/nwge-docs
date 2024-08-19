# Nwge Bundle Format Specification, Version 1

This document shall serve as the specification of the bundle file
format used by the nwge engine. It contains both the formal definition and
C-like pseudo-code.


## File Structure

The file consists of the following:

* [Magic Value](#magic-value), always at offset `0`
* [Header](#header), always at offset `8`
* [File Tree](#file-tree), offset determined by header: see below
* File Data

## Magic Value

The first eight bytes of a bundle file consist of a magic number, where:

* The lowest 7 bytes must be equal to the ASCII byte sequence `NWGEBND`
  (`0x444E424547574E`).

* The topmost byte must be equal to the version, which is `1`.

```c
//N  W  G  E  B  N  D\x01
0x4E'57'47'45'42'4E'44'01
```

## Header

The bundle header consists of two fields:

* The 4-byte offset into the file at which the File Tree is stored. The official
  `libnwge_bndl` implementation ensures the File Tree offset is aligned to 16
  bytes (divisible by 16). This not required.

* 4 bytes of padding. The official `nwgebndl` tool writes the ASCII byte
  sequence `nwge` (`0x6567776E`) here.

```c
struct Header {  // always at offset 8
  u32 treeOffset;
  u32 padding;
};
```

## File Tree

The file tree consists of:

* The 4-byte file count.

* File information for each file stored consecutively.

```c
struct FileTree {  // offset determined by Header's treeOffset
  u32 fileCount;
  FileInfo files[fileCount];
};
```

## File Info

The file info consists of:

* 12-byte file name. In case the name is shorter than 12 characters, it must be
  padded with zeroes. The name must be stored in all uppercase.

* 4-byte file extension. Alike the file name, if the extension is shorter than 4
  characters, it must be padded with zeroes. The extension must be stored in all
  uppercase.

* 4-byte size of the file data.

* 4-byte offset into the bundle file at which the file's data is stored.

```c
struct FileInfo {
  char name[12];
  char extension[4];
  u32 size;
  u32 offset;
};
```

## File Data

The raw data of each file stored in the bundle. The official `nwgebndl` tool
simply stores all files after each other between the Header and the File Tree.
It is permissible to store file data after the File Tree. It is permissible to
store file data in both locations at once. It is permissible for file data to
overlap. It is permissible for file data to extend into the bundle file
structures, as bundle files are inherently immutable. It is permissible for the
files' data to not be stored contiguously -- i.e. with padding added between
files. The official `libnwge_bndl` implementation adds padding between files to
ensure data is aligned to 16-byte boundaries.

## Example File

Below is a commented hex dump of the
[`BUNDLEv1EXAMPLE.BNDL`](bin/BUNDLEv1EXAMPLE.BNDL) file.

```c
/* Magic at offset 0 */
0x4E'57'47'45'42'4E'44'01 // "NWGEBND\x01" or 0x01444E424547574E

/* Header at offset 8 */
0x16'00'00'00 // File Tree Offset: 0x16
0x6E'77'67'54 // Padding: "nwge" or 0x6567776E

/* File Data */
0x48'65'6C'6C'6F'2E // PLAIN.TXT data: "Hello."

/* File Tree at offset 0x16 */
0x01'00'00'00 // File Count: 1

  /* File #1 */
  0x50'4C'41'49'4E'00'00'00'00'00'00'00 // Name: "PLAIN"
  0x54'58'54'00 // Extension: "TXT"
  0x06'00'00'00 // Size: 0x06
  0x10'00'00'00 // Offset: 0x10
```
