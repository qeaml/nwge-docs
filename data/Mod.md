# Mod

A **mod** allows the end user of a nwge game to replace files in the game's
bundles.

## Structure

A mod consists of folders that match names with bundles used by the game. The
files contained in these folders will be used in place of the original files
stored in the respective bundle.

Each mod is contained in its own folder. Each mod's folder is placed in the
`mods` folder in the game's installation folder.

For example, for a mod named `hard-mod` to replace `config.json` in the
`game.bndl` bundle:

`<game installation folder>/mods/hard-mod/game/config.json`

Alike bundles, the names of the files in the mod are case-insensitive regardless
of the host system.

## Restrictions

It is not possible for mods to change engine or game behavior. Mods can only
replace files present in bundles. It is up to the developer of the game to
provide the user with a way to modify the game's behavior via overriding files
in the game's bundles. Developers are encouraged to use JSON files in their
bundles to allow the user to modify game behavior.

## See Also

* [Bundle](Bundle)
