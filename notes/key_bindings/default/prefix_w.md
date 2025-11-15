## `prefix + w`

### Executive Summary

The `prefix + w` keybinding in tmux is used to list all the windows within the current session and present them in a chooser interface, allowing the user to quickly navigate and switch to any window. This enhances session navigation by providing a visual list, which is particularly useful when many windows are open.

### Keybinding Definition

* **Keystroke Sequence:**  
  `C-b` (default prefix) followed by `w`  
* **Default/Custom:**  
  **Default**; this binding is part of tmux’s standard keymap.

### Functional Description

When a user presses the tmux prefix key (`Ctrl + b` by default) and then `w`, tmux invokes the `choose-window` command. This command pops up a list of all existing windows in the current session. Each entry shows the window’s index and name, which helps the user to identify and select the desired window. This chooser is modal; once the `choose-window` list appears, the user can navigate within it (typically using arrow keys or vi-style keys) and press Enter to switch to the selected window.

### Related Keybindings

- **`prefix + n`**: Moves to the *next* window in the session.
- **`prefix + p`**: Moves to the *previous* window.
- **`prefix + <number>`**: Directly jump to a window by its index (e.g., `prefix + 1` goes to window 1).
- **`prefix + ,`**: Rename the current window.
- **`prefix + &`**: Kill (close) the current window.

---
