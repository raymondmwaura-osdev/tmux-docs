# `base-index`

## Overview

* The `base-index` variable in tmux controls the starting index for numbering new windows created in a session.
* Its role is to define from which integer tmux will begin assigning window-numbers within a given session; i.e. the first window created will take the “base” number, the next will be base + 1, and so on.
* Default value: `0` (i.e. window-index numbering normally starts at 0).
* **scope**: session-specific (an option per session).

---

## Accepted Values / Data Type

* Data type: integer.
* Acceptable values: any non-negative integer (0, 1, 2, …). In typical use one selects 0 or 1.
* Constraints / formatting: no fractional or negative values; must be an integer.
* Edge cases / considerations:

  * Setting the value to 0 yields the default behavior (windows numbered from 0).
  * Setting to 1 (or higher) shifts window numbering accordingly; e.g. first window becomes 1.
  * Using a non-zero base means that any scripts or keybindings referring to window indices must account for the offset.

---

## Example

To configure tmux so that windows start numbering from 1, you would set `base-index` in your tmux configuration file (e.g. `~/.tmux.conf`). Example:

* Short / config-file syntax:

```text
set -g base-index 1
```

* Equivalent full-form `set-option`:

```text
set-option -g base-index 1
```

After reloading the configuration (or starting a new session), the first window created will be numbered `1`, the next `2`, and so on. This is commonly paired with setting `pane-base-index` so that pane numbering also begins at 1.

If you later wish to revert to default (0-based numbering), you can either remove the line or explicitly set:

```text
set -g base-index 0
```

---
