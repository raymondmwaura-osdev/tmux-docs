# `new-window` (alias `neww`)

## Overview

The `new-window` command in tmux is used to create a new window within a given session. In tmux’s architecture, sessions are containers of windows, and windows themselves host one or more panes (shells or programs). Thus `new-window` is a workspace-resources manipulation command: it allocates a new window inside a session (and optionally runs a command in it), extending the “window list” of that session. It does not affect other sessions, nor directly manage panes (unless the command run creates panes).

When invoked, tmux’s server updates the session’s window list; the newly created window becomes part of the session’s resources. Depending on options, the new window may be selected (i.e. become the current window) or created “silently” in the background.

---

## Canonical Syntax Pattern

```
new-window [ -d ] [ -n window-name ] [ -t target-window ] [ -c start-directory ] [command]
```

Short alias:

```
neww [options] [command]
```

where:

* `-d`; create the window but do not make it the current (active) window
* `-n window-name`; assign a name to the new window
* `-t target-window`; designate the target session/window container to receive the new window (e.g. `session:` or `session:window-index`)
* `-c start-directory`; set the working directory for the new window
* Optional `[command]`; a shell command (or program) to run inside the new window. If omitted, tmux runs the default shell/command.

---

## Typical Use Cases

* Creating a fresh shell window while inside a tmux session; e.g. you want to start a new task in its own full-terminal context.
* Launching a new window running a specific command, e.g. a monitoring tool (`top`, `htop`), editor, or a long-running process.
* Creating a new window but leaving the focus on the current one (background window), useful for starting background tasks without interrupting the current workflow (`-d` flag).
* Naming windows explicitly so that navigation and identification among multiple windows becomes easier.
* Creating a window in a specific working directory (e.g. project directory), useful when you want to isolate work per project/environment.

---

## Behavioral Notes

* `new-window` manipulates **ephemeral runtime state**: it changes the active session’s window list. It is not a configuration setting (unless scripted in configuration files), so windows exist only for the lifetime of the session or until explicitly closed.
* By default, the newly created window becomes the current (active) window. Using `-d` prevents focus switching.
* When assigning a `target-window` with `-t`, you may specify a session (or session:name); if unspecified, the “current session” is assumed.
* If a command is supplied, tmux runs it in the new window. If no command is given, tmux runs the default shell (or the shell defined by default-command option).
* The working directory of the new window can be controlled via `-c`, which is useful to inherit or override the default path.
* When the command running in the new window terminates, the window closes; unless other settings (like `remain-on-exit`) are configured.

---

## Minimal Working Example

```bash
# Create a new window in the current session, run bash shell (default)
tmux new-window

# Create a new window named "editor" and run vim
tmux new-window -n editor "vim"

# Create a new window in background (do not switch to it), in /home/user/project
tmux new-window -d -c /home/user/project -n project-shell
```

In these examples:

* The first creates a new shell window and switches to it.
* The second creates a window running `vim`, named “editor”.
* The third creates a window named “project-shell” rooted at `/home/user/project`, but leaves focus in the current window.

---
