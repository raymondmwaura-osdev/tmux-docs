## `prefix + s`

### Executive Summary

The `prefix + s` keybinding in **tmux** invokes an interactive *tree-view mode* (via the `choose-tree` command) that displays all tmux **sessions**, **windows**, and **panes**. It allows the user to navigate, select, kill, or apply commands to these items in a structured, hierarchical representation.

---

### Keybinding Definition

* **Keystroke Sequence:**
  `prefix` (by default `C-b`) followed by `s` (i.e., `C-b s`)

* **Default/Custom:**
  **Default** in tmux (i.e., it is bound by default in most tmux installations)

---

### Functional Description

When the user presses `prefix + s`, tmux executes the command:

```
choose-tree -s
```

This opens a *tree mode* interface, where each **session** is initially shown in a *collapsed* form (`-s` flag collapses sessions), allowing the user to expand them if desired.

In this tree mode, the following key actions are available **without needing to re-press the prefix**: navigation via `Up`/`Down` arrows, expanding or collapsing nodes with `+` / `-`, tagging items with `t`, killing items with `x` (or `X` for all tagged), and entering a format-filter or other command via `:`.

Once a user selects an entry (e.g., by pressing *Enter*), tmux switches the current client to the chosen session/window/pane (depending on the level picked) using `switch-client -t '%%'` as the template when no explicit template is provided.

This interactive tree allows previewing of panes/windows/sessions (in the lower half of the screen), making it easier to understand the layout and context before selecting.

---

### Interoperability Considerations

1. **Overlap with `prefix + w`:**

   * The default tmux binding for `prefix + w` also calls `choose-tree`, but with windows expanded by default (i.e., `choose-tree -w`).
   * Because both `s` and `w` invoke tree mode, but with different initial expansion (sessions vs windows), users should be aware that reconfiguring one may affect expectations of hierarchical navigation.

2. **Conflict Avoidance:**

   * If a custom `.tmux.conf` rebinds `s` in the `prefix` key table, it may override the default interactive tree behavior.
   * Some users rebind `s` to `choose-session` instead; this simplifies the interface to only list sessions, but removes window/pane context.

3. **Custom Formatting:**

   * The `-F` flag to `choose-tree` allows custom formats for how each line is displayed (e.g., embedding session metadata, notes).
   * Users integrating tmux with plugins or scripting might override the default behavior by providing their own template to `choose-tree`, changing what happens when *Enter* is pressed (e.g., kill a session instead of switching).

---
