# `bind` (alias `bind-key`)

## Overview

The `bind` command in tmux serves to attach a tmux command to a specific keystroke (or mouse event) within tmux’s key-binding infrastructure. Its role is to map user input (key or mouse) to tmux operations, effectively customizing how a user interacts with the tmux environment. This does not manipulate sessions, windows, or panes directly; instead, it configures which keys trigger which operations, thus shaping user input handling.

In tmux’s architecture, the command affects the **client’s input handling layer**, by registering key-to-command bindings. It does not by itself create or destroy sessions/windows/panes; but the commands bound may do so when invoked.

---

## Canonical Syntax Pattern

```
bind-key [-nr] [-N note] [-T key-table] key command [arguments]
```

(Short alias: `bind`; equivalent to `bind-key`.)

* `key`: the keystroke (or mouse event) to bind.
* `command [arguments]`: the tmux command (and optional parameters) to be invoked when the key is pressed.
* Optional flags:

  * `-n` (alias for `-T root`); bind in the root table (i.e. without requiring the prefix key).
  * `-T key-table`; bind in a specific key-table (e.g. `copy-mode`, custom tables).
  * `-r`; allow binding to be repeatable (i.e. pressing key repeatedly triggers the command multiple times, without re-invoking prefix).
  * `-N note`; attach a note/help string to the binding (visible via listing).

---

## Typical Use Cases

* Defining custom keyboard shortcuts in `~/.tmux.conf`; e.g., binding `C-t` to create a new window, instead of the default.
* Overriding or removing default key bindings; e.g., reassigning the prefix from `C-b` to `C-a`.
* Binding keys to non-default commands for convenience; e.g., quickly splitting panes, switching windows, resizing panes, or invoking custom scripts.
* Binding mouse events (if mouse mode enabled); e.g., clicking a pane border to select or resize panes.
* Defining repeatable commands; for operations like resizing panes continuously by holding a key.

---

## Behavioral Notes

* Bindings configured via `bind` alter **runtime input handling**. If placed in the configuration file (`~/.tmux.conf`), they apply at startup and persist (until changed).
* By default, bindings are added to the **prefix table**; meaning the user must press the prefix key (e.g. `C-b`) before the bound key.
* Using `-n` / `-T root` binds keys without requiring the prefix; this is more global and may conflict with shell/terminal keys; caution advised.
* The `-r` (repeat) flag enables the key to fire repeatedly if the key is held or pressed quickly in succession, subject to tmux’s `repeat-time` settings.
* If a key already has a binding, `bind` will **replace** the existing binding. For completely removing a binding, `unbind` (alias `unbind-key`) is used.
* Bindings apply per key-table. tmux supports multiple tables (e.g. `prefix`, `root`, `copy-mode`, and custom tables). This allows context-sensitive key behavior.

---

## Minimal Working Example

```text
# In ~/.tmux.conf or at tmux command prompt
bind-key C-t new-window
```

This binding makes pressing the prefix key (e.g. `C-b`), then `C-t`, create a new tmux window.

---
