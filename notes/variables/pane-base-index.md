# `pane-base-index`

## Overview

* The `pane-base-index` variable in tmux controls the starting index for numbering panes within each window.
* Its role is to define the offset used when tmux assigns pane numbers: the first pane in a window will take the “pane-base” number, the next will be base + 1, etc. This affects how panes are referenced in commands or when listing pane indices.
* Default value: `0`. By default, pane numbering starts from zero.
* **scope**: window-specific (the setting applies per window option, though often configured globally via window-option defaults).

---

## Accepted Values / Data Type

* Data type: integer index.
* Acceptable values: integers between **0 and 255** inclusive.
* Constraints / formatting: must be a non-negative integer in the allowed range.
* Edge cases / considerations:

  * Setting to `0` restores default behavior (zero-based pane indexing).
  * Setting to `1` (or higher) shifts pane numbering accordingly; scripts or commands that refer to pane indices must account for the offset.
  * Because pane indexing is per-window, pane numbering resets when you open a new window.

---

## Example

To configure tmux so that panes start numbering from 1 (rather than 0), one typically sets `pane-base-index` in the tmux configuration file (e.g. `~/.tmux.conf`), often in conjunction with window-option syntax. For example:

* Global config (affects all windows):

```text
setw -g pane-base-index 1
```

* Full equivalent syntax using `set-window-option`:

```text
set-window-option -g pane-base-index 1
```

After reloading the configuration (or opening a new tmux session/window), panes within windows will be numbered starting at 1. For example, the first pane will be `.1`, the next `.2`, etc.

If later you want to revert to zero-based pane numbering:

```text
setw -g pane-base-index 0
```

---
