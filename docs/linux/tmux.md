---
title: TMUX Cheatsheet
date: 2024-06-11
---

# TMUX Cheatsheet

### Prefix Key

`Ctrl+b`

### Session Management

`tmux new -s session_name` - New session with Name

`d` - Detach from session

`tmux ls` - List sessions

`tmux a` - Attach to session

`tmux a -t number/name` - Attach to specific session

`tmux new -s session_name` - New session

`tmux rename-session name` or `Prefix+$` - Rename current session

`Prefix+s` - List sessions within tmux

### Split Actions

`Shift+5` - Vertical Split

`Shift+"` - Horizontal Split

`Prefix+ArrowKeys` - Move panes

`Prefix+X` - Kill current Pane

### Windows

`Prefix+c` - New window

`Prefix+p` - Previous Window

`Prefix+n` - Next Window

`Prefix+WindowNumber` - Switch to Window

`Prefix+Comma` or `tmux rename-window name` - Rename window

`Prefix+Shift+7` - Kill Window

`Prefix+w` - List all windows

`Ctrl+D` - General linux command. Exit a pane/window in tmux

`tmux new -s mysession -n mywindow` - Start new session with new window

### Commands

`Prefix+:` - Command mode