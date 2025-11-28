# `source-file`

## Overview

The `source-file` command in tmux provides a mechanism to load and execute a sequence of tmux commands from an external file at runtime. Conceptually, it allows the tmux server to re‑evaluate configuration or scripted commands without requiring a full restart or re‑creation of sessions, windows, or panes. Through `source-file`, one can dynamically adjust behavior or workspace configuration (for instance updating bindings, layout rules, or other settings) by applying the content of a file against the running tmux environment.

In terms of tmux’s architecture (server, sessions, windows, panes, clients), `source-file` works at the server level (or global context). It does not directly manipulate panes or windows as its core action; instead, it injects commands that may themselves act on sessions/panes/windows. Thus `source-file` is a configuration‑loading tool rather than a direct workspace‑manipulation primitive.

---

## Canonical Syntax Pattern

```
source-file <path>
```

Optionally, when using the alias:

```
source <path>
```

where `<path>` is a filesystem path (absolute or relative) pointing to a file containing tmux commands.

---

## Typical Use Cases

* Reloading a personal configuration file (e.g. `~/.tmux.conf`) after editing it, without restarting tmux.
* Applying additional configuration snippets or supplemental tmux‑script files during a running session (e.g. splitting windows, setting up custom layouts, key‑bindings, or other workspace initialization).
* Sharing configuration across multiple machines or tmux sessions by sourcing a common config file, facilitating consistent environment setup.
* Quickly toggling or switching between different tmux configurations by sourcing different files as needed.

---

## Behavioral Notes

* The commands inside the sourced file are executed in sequence, as if they had been typed manually in tmux command mode.
* `source-file` affects the **live** state of the tmux server: new settings (key‑bindings, options, layout commands, etc.) take effect immediately; there is no need to restart or recreate sessions.
* However, `source-file` does **not** reset or revert prior changes; it simply executes additional commands. If a config option was changed previously, sourcing will overlay or overwrite, but it will not restore default behavior unless the file does so explicitly.
* Because `source-file` operates globally (via the server), it's not scoped to a single session, window, or pane; configuration changes apply broadly.
* Useful especially for reloading `.tmux.conf` after edits, avoiding the need to kill or restart the tmux server and lose session state.

---

## Minimal Working Example

Suppose you have edited your tmux configuration file at `~/.tmux.conf`. To apply the changes without restarting tmux, run:

```bash
tmux source-file ~/.tmux.conf
```

Alternatively, if already inside a tmux session, press the prefix (default `Ctrl+b`), then type:

```
:source-file ~/.tmux.conf
```

This will reload the configuration, applying any changed options or new key‑bindings defined in that file.

---
