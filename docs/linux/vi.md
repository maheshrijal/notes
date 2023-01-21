---
title: VI Edtitor cheatsheet & Commands
date: 2023-01-21
---
# VI Editor :heart:

### Save a file in vi without sudo
```
:w !sudo tee %
```

### Input/Append text VI
- `a` - Append after the cursor
- `i` - Append before the cursor
- `I` - Append at the beginning of a line
- `A` - Append at the end of a line
- `o` - Create new line under the cursor
- `O` - Create new line above the cursor

### Cursor Movement in VI
- `gg` Begning of the file
- `G` Move to the last line in the file
- `5G`Move to line 5 of the file (5 can be any line number)
- `H` Upper left corner (home) of the current screen
- `M` Middle line
- `L` Lower left corner
- `^` Beginning of line
- `$` End of line
- `l` Forward a character
- `w` One word forward
- `b` Back one word

### Vi Commands
- `u` Undo last change
- `U` Undo all changes on line
- `:w !sudo tee %` Save a file that is opened without sudo
- `dd` Delete current line
- `ndd` Delete n lines of buffer
- `%d` Delete all lines in a file

### Search and Replace

Syntax: `:[address]s/old_text/new_text/`

- `:%s/foo/bar/g` Replace foo with bar globally
- `:s/foo/bar/g` replace all matches (`g` flag) of 'foo' with 'bar' on the current line only
- `:%s/foo/bar/gc` Replace foo with bar but confirm for every change
- `:s/foo/bar/` replace the first match of 'foo' with 'bar' on the current line only

#### Address components
| Address      | Description                          |
| ----------- | ------------------------------------ |
| `.`       | Current line  |
| `n`       | Line number |
| `$`    | Last line |
| `n.+m`    |Current line plus m lines |
| `/string/`    | A line that contains "string" |
| `%`    | Entire file |
| `[addr1],[addr2]`   | Specifies a range |


## VI Commands

- `:set number` Show line numbers
- `:set list` Show invisible characters