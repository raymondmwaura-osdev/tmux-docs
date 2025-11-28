# `pane_current_path`

## Overview

- The variable `pane_current_path` in tmux represents the **current working directory** of a given pane (if tmux is able to determine it).
- This variable is used primarily in tmux *formats* (e.g. status‑bar strings, window/pane naming, or commands) to dynamically reflect the directory of the pane in question.
- By itself, `pane_current_path` does **not** correspond to a “setting” that tmux persists or stores as a user‑modifiable option; it's a read-only format variable that reflects runtime state.  
- Therefore, there is **no default “value”** in the sense of a config default; if the pane has a working directory, that directory path is returned; if tmux cannot detect it (e.g. after a process exit or for some shells/environments), the value may be empty or undefined.  
- **Scope**: pane‑specific (i.e. each pane may have its own current path).  

---

## Accepted Values / Data Type

- **Data type**: String. `pane_current_path` yields a string representing a filesystem path (e.g. `/home/user/project`).
- **Acceptable values**: Any valid absolute (or possibly relative, depending on shell) directory path accessible from the environment of that pane. There is no special enumerated set; it's simply whatever the underlying shell or process reports as the current working directory.  
- **Constraints / formatting rules**:  
  - Path syntax should follow the operating system’s normal filesystem path format (e.g. POSIX-style on Unix-like OS).  
  - If the pane’s working directory is unavailable (e.g. pane process terminated, no shell running, or shell does not report a working directory), the variable may be empty or undefined.  
  - There is no explicit “null” value in tmux’s manual; absence of a valid path implies empty output.  

---

## Example

Given that `pane_current_path` is a format variable (not a set‑option), you typically use it in commands or status‑line formats rather than “set” it directly. Below are example usages.

- **Short-form (status line / format usage):**

```tmux
# In ~/.tmux.conf; show current pane working dir in status-right
set-option -g status-right '#{pane_current_path} %H:%M'
```

* **Long-form (e.g. in key binding for new windows / splits):**

```tmux
# Create new pane/window inheriting current pane's directory
bind c new-window -c "#{pane_current_path}"
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"
```

This ensures that when you open a new window or split a pane, the new shell starts in the same working directory as the pane from which you invoked the command.

---
