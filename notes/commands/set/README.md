# `set` (alias for `set-option`)

## Overview

The `set` command in tmux is used to configure internal options that govern the behavior of the tmux server, sessions, windows, or panes. Rather than directly creating or destroying workspace resources (like sessions or panes), `set` modifies how tmux behaves: for example, how windows are numbered, what the status bar looks like, whether mouse support is enabled, or clipboard behavior. It operates within tmux’s abstraction of server, session, window, pane and influences how tmux manages and presents these entities; effectively customizing the runtime or startup environment.

---

## Canonical Syntax Pattern

```
set [ -a | -g | -s | -w | -p | -u | -q ] [ -t target ] option value
```

In less verbose form (with default assumptions):

```
set option value
```

* Without explicit scope flags, tmux infers whether the option is a session, window, or pane option.
* Scope modifiers (`-g`, `-s`, `-w`, `-p`) let you target global defaults, server-wide settings, or specific window/pane/session contexts.

---

## Typical Use Cases

* Defining preferences in a configuration file (e.g. `~/.tmux.conf`) so that every tmux session launches with consistent settings (prefix key, mouse mode, color scheme, indexing, status bar behavior).
* Temporarily toggling settings at runtime (e.g. enabling/disabling mouse mode, changing history-limit, adjusting status bar properties) without restarting tmux.
* Modifying window or pane-level behavior (e.g. style, color, renaming, pane-based options) for one particular window/pane rather than globally.
* Using “user options” (custom options prefixed with `@`) to store arbitrary metadata or values to be accessed by tmux scripts or plugins.

---

## Behavioral Notes

* **Persistent vs. ephemeral:** If used in a tmux configuration file (`~/.tmux.conf`), `set` defines persistent behavior applied on all new sessions. If used at the command prompt within tmux, its effects apply only to the current server or session, and will revert when tmux restarts.
* **Scope and inheritance:** tmux maintains hierarchical option scopes. Global server options affect the entire server. Global session or window options act as defaults for sessions or windows that don’t override them. When you set an option for a session or window (without `-g`), only that session/window is affected; others revert to global defaults if not explicitly set.
* **Appending vs. overwriting:** The `-a` flag appends new value to existing option (when option expects a string or style). Without `-a`, the old value is overwritten.
* **Unsetting options:** The `-u` flag removes a previously set option so that the configuration falls back to inherited defaults (global or tmux default).
* **Conditional/no-error handling:** The `-o` flag can prevent setting an option if already set; `-q` suppresses warnings about unknown or ambiguous options.

---

## Minimal Working Example

```bash
# In ~/.tmux.conf; make the prefix key Ctrl-a globally
set -g prefix C-a

# Also enable mouse support globally
set -g mouse on

# Use 256-color default terminal for all windows
set -g default-terminal "screen-256color"
```

Or interactively, inside a running tmux session (press prefix + `:`):

```
set -g mouse on
```

This will enable mouse support for the current server session.

---
