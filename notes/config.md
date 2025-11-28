# Tmux config

## What is tmux / why configure it

* tmux is a *terminal multiplexer*, allowing you to run multiple terminal sessions inside one terminal window, split windows into panes, detach/reattach sessions, and manage workspaces.
* The default key bindings and behavior are general-purpose, but customizing tmux can significantly improve your workflow, especially if you regularly use multiple panes, frequent context-switching, or remote sessions. Configuration transforms tmux from a generic tool into a personalized, efficient environment.

---

## Location and mechanism of configuration

* tmux reads configuration from a file named `.tmux.conf` in your home directory (i.e. `~/.tmux.conf`).
* Alternatively, there can be a global configuration file (e.g. `/etc/tmux.conf`).
* The content of `.tmux.conf` consists of tmux commands (same commands you would type in the tmux prompt or shell) that are applied when tmux server starts.
* If tmux is already running, changes to `.tmux.conf` won’t take effect automatically; you must either reload the configuration (via a tmux command), or restart your tmux server for full effect.

---

## Basic and recommended tmux configuration settings

Here are some common configuration directives placed in `.tmux.conf`, plus rationale for each.

```text
# Example ~/.tmux.conf
```

### Prefix key remapping

By default, tmux uses `Ctrl + b` as the “prefix” (the modifier you press before issuing tmux commands). Many users find this awkward, and remap it. For example:

```
unbind C-b
set -g prefix C-a
bind-key C-a send-prefix
```

This changes the prefix to `Ctrl + a`.

### Pane / window numbering from 1 instead of 0

Default numbering in tmux starts at 0, which some find unintuitive. To make numbering start at 1:

```
set -g base-index 1
setw -g pane-base-index 1
```

This way, Window “1” is your first window, matching more natural mental models.

### Enable mouse support

If you prefer using a mouse (for scrolling, resizing panes, switching windows) rather than always keyboard:

```
set -g mouse on
```

This turns on mouse interactions inside tmux.

### Reload configuration without restarting tmux

Instead of shutting down tmux to apply changes, you can bind a key to reload the config file:

```
bind-key r source-file ~/.tmux.conf \; display-message "Config reloaded!"
```

With this, pressing `Prefix + r` will reload `.tmux.conf` and show a confirmation; useful when iterating on config tweaks.

---

## More advanced customizations (status bar, appearance, behavior)

Once you have the basics working, many users extend their `.tmux.conf` to customize appearance and behavior:

* Change status-bar colors or content (for example, to show session name, current directory, git branch, system load, etc.)
* Adjust default terminal type to ensure better compatibility or color support (e.g. `set -g default-terminal "screen-256color"`)
* Customize pane navigation: bind keys to move between panes (e.g. vim-style hjkl navigation) rather than default arrow-key or prefix-based switching.
* Configure copy/paste behavior, clipboard integration (especially on Linux + X11, sometimes requiring external utilities); though this often needs extra support beyond pure tmux config.

---

## Workflow and best practices

* Maintain a single `.tmux.conf` per user, version-controlled (e.g. in your dotfiles repo). Treat it like your shell rc or editor config.
* When experimenting, use the reload-shortcut (`Prefix + r`); this avoids restarting sessions and speeds iteration.
* Choose prefix and keybindings carefully: avoid conflicts with shell shortcuts, remote shells, or commands inside panes.
* Keep your config modular and commented: use `#` for comments, grouping related settings (navigation, appearance, behavior). This aids maintainability and readability.

---

## Example minimal `.tmux.conf` template

```text
# ~/.tmux.conf

# Remap prefix to Ctrl-a
unbind C-b
set -g prefix C-a
bind-key C-a send-prefix

# Number windows/panes starting at 1
set -g base-index 1
setw -g pane-base-index 1

# Enable mouse support
set -g mouse on

# Reload config with prefix + r
bind-key r source-file ~/.tmux.conf \; display-message "Config reloaded!"

# Optional: set default terminal
set -g default-terminal "screen-256color"

# Optional: status bar styling (as example)
set -g status-bg colour235
set -g status-fg colour136
set -g status-left-length 40
set -g status-right-length 90
set -g status-left "#[bold]#S"
set -g status-right "#(date +'%Y-%m-%d %H:%M')"
```

---
