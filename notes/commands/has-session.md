# `has-session` (tmux has-session / tmux has)

## Overview

The `has-session` command provides a way to test whether a named (or numbered) session already exists in the tmux server. Rather than creating, attaching, or modifying sessions, it performs a simple existence check. In tmux’s architecture (which distinguishes between server, sessions, windows, panes, and clients) `has-session` queries the server’s list of sessions. Depending on whether the specified session is present, the command exits with a status code indicating success or failure. This makes it useful for conditional logic in scripts or automation, where subsequent actions may depend on whether a session exists.  

Thus, `has-session` does not affect persistent configuration or change runtime state; it merely inspects the current state of sessions.

---

## Canonical Syntax Pattern

```bash
tmux has-session [-t target-session]
```

Or using its alias:

```bash
tmux has [-t target-session]
```

**Parameters:**

- `-t target-session`: Specifies the session name (or numeric session identifier) to check. If omitted, tmux will report an error (because you must specify which session you are querying).

---

## Typical Use Cases

- **Script-based session creation guard**; Before creating a session, a script can check if a session with a given name already exists; if so, skip creation to avoid “duplicate session” errors.  
- **Conditional re-attach or new-session logic**; In a shell script or login profile: if a session exists, attach to it; otherwise, create a new session.  
- **Automation workflows**; For automated task runners or deployment scripts that use tmux: check for session existence before attempting to attach, kill, or reuse.  
- **Session existence validation**; As a precondition check: verify that the expected session is present before performing operations (e.g. sending commands to that session).  

---

## Behavioral Notes

- The command returns an **exit status code**, not interactive output: **0** if the specified session exists; **1** (and prints an error message) if it does not.
- It does **not** create, attach, detach, or otherwise modify sessions. It purely tests state.  
- Ideal for scripting / automation, because you can combine it with shell logic to guard subsequent tmux commands.  
- Because it does no side-effects, it is safe to call repeatedly: it doesn’t change tmux server state or client bindings.  
- If used with a session identifier that refers to nowhere, tmux will return an error; so robust scripts should check its exit code and avoid blindly assuming success.  

---

## Minimal Working Example

Here's a simple shell snippet illustrating how `has-session` might be used in practice:

```sh
#!/bin/sh

SESSION="mywork"

if tmux has-session -t "$SESSION" 2>/dev/null; then
  # Session exists; attach to it
  tmux attach -t "$SESSION"
else
  # Session does not exist; create and attach
  tmux new-session -s "$SESSION"
fi
```

In this example, the script checks for the existence of a session named `mywork`. If present, it attaches; otherwise, it creates one. This avoids “duplicate session” errors, and ensures a usable tmux workspace regardless of prior state.

---
