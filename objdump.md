# objdump

`objdump` can be used to examine the makeup of a library `.a` file.

## Common commands

The most used option will likely be the `-t` option followed by the file you wish to examine. The output will have the following format.

### Groups for Flags

- `l`, `g`, `u`, or `!`
  - Used for local, global, unique global, a space for neither, or `!` for both.
- weak `w` or strong (space)
- `C` for constructor or space for ordinary
- `W` for warning or space for regular symbol
