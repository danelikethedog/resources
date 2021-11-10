# VIM

## Normal Mode

`i` for insert mode

`d` with motion deletes text that motions over

`ciw` change inner word

`cw` change word

`x` deletes char under the cursor

`R` replace mode

`v` for visual mode

`V` for visual line mode

`Ctrl-v` for visual block mode

`$` goto end of line

`:tabnew` for new tab to edit in

`:tabedit <file>` to open new file in new tab

`:5,12s/foo/bar/gc` to find and replace with confirmation

`Ctrl-]` to go to definition with ctags

`Ctrl-T` to go back from definition

## Text Entry Commands (Used to start text entry)

`a` Append text following current cursor position

`A` Append text to the end of current line

`i` Insert text before the current cursor position

`I` Insert text at the beginning of the cursor line

`o` Open up a new line following the current line and add text there

`O` Open up a new line in front of the current line and add text there

## Cursor Movement Commands

The following commands are used only in the commands mode.

`h` Moves the cursor one character to the left

`l` Moves the cursor one character to the right

`k` Moves the cursor up one line

`j` Moves the cursor down one line

`nG` or `:n` Cursor goes to the specified (n) line (ex. 10G goes to line 10)

`^F` (CTRl F) Forward screenful

`^B` Backward screenful

`^f` One page forward

`^b` One page backward

`^U` Up half screenful

`^D` Down half screenful

`$` Move cursor to the end of current line

`0` (zero) Move cursor to the beginning of current line

`w` Forward one word

`b` Backward one word

## Exit Commands

`:wq` Write file to disk and quit the editor

`:q!` Quit (no warning)

`:q` Quit (a warning is printed if a modified file has not been saved)

`ZZ` Save workspace and quit the editor (same as :wq)

`:` 10,25 w temp

`write` lines 10 through 25 into file named temp. Of course, other line numbers can be used. (Use :f to find out the line numbers you want.)

## Text Deletion Commands

`x` Delete character

`dw` Delete word from cursor on

`db` Delete word backward

`dd` Delete line

`d$` Delete to end of line

`d^` (d caret, not CTRL d) Delete to beginning of line

## Yank (has most of the options of delete)-- VI's copy commmand

`yy` yank current line

`y$` yank to end of current line from cursor

`yw` yank from cursor to end of current word

`5yy` yank, for example, 5 lines

## Paste (used after delete or yank to recover lines.)

`p` paste below cursor

`P` paste above cursor

`2p` paste from buffer 2 (there are 9)

`u` Undo last change

`U` Restore line

`J` Join next line down to the end of the current line

## File Manipulation Commands

`:w` Write workspace to original file

`:w file` Write workspace to named file

`:e file` Start editing a new file

`:r file` Read contents of a file to the workspace
