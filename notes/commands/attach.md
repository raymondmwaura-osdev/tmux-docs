# `attach` (tmux attach / tmux attach‑session)

## Overview

The `attach` command is used within tmux to connect a client (i.e. a terminal instance) to an existing tmux session. In tmux’s architecture, a “session” groups windows (and their panes), while a “client” represents a terminal interface through which a user interacts. The `attach` command binds a client to a session, allowing the user to resume or share an existing workspace. It does not create new sessions or windows; rather, it operates on workspace resources already managed by the tmux server, restoring user interaction to a session that is detached or previously running.  

Thus, `attach` manipulates runtime workspace state (client‑to‑session association), rather than persistent configuration. It allows re-entry into an existing session, preserving windows, panes, running processes, and session context.  

---

## Canonical Syntax Pattern

```bash
tmux attach-session [‑dErx] [‑c working-directory] [‑t target-session]
```

`attach` is an alias for `attach-session`.  

**Flags / parameters:**

- `-t target-session`: Specifies the name or numeric identifier of the session to attach to. If omitted, tmux selects the most recently used session that is currently not attached to any client.
- `-d`: When provided, tmux will first detach any other clients attached to that session, then attach the current client. Useful when you want exclusive control over the session.
- `-r`: Attach in read-only mode. The client can view output but cannot send input (i.e. cannot interact).
- `-c working-directory`: Sets the working directory for the session (affects where new windows within that session will start).
- `-E`: Prevents applying the “update-environment” option (i.e. environment updating).
- `-x` (undocumented in many informal tutorials): available for advanced usage (less common for novices).

Aliases / shortcuts:  

- `tmux attach` and `tmux a` are common shortcuts.

---

## Typical Use Cases

1. **Re‑connecting after disconnection**; If a tmux session is left running (e.g. after closing a terminal or losing an SSH connection), `attach` lets you re‑enter that session and continue where you left off.
2. **Switching between multiple sessions**; When multiple sessions exist, you can attach to a specific session by name or ID using `-t`.
3. **Session sharing / multi‑client access**; Multiple clients (terminals) can attach to the same session (unless forcibly detached). This is useful for sharing a workspace or collaborating across terminals.
4. **Forcing exclusive control**; Using the `-d` option to detach other clients, then attach ensures you regain exclusive terminal control of a session (e.g. if someone else was connected).
5. **Remote workflow continuity over SSH**; When connecting to a remote machine over SSH, you can re‑attach to a tmux session to resume long-running tasks without losing state.

---

## Behavioral Notes

- The command affects **ephemeral runtime state**; it does not modify tmux configuration files, only the current client-session linkage.  
- It operates at the **session scope**: it selects a session to attach a client to, rather than acting on a window or pane.  
- If no session name/ID (`-t`) is specified, tmux attaches to the most recently used session that is currently not attached to any client.
- By default, tmux allows **multiple clients** to attach to the same session (i.e. shared session).
- The `-d` option changes this: it will detach all other clients attached to the session, giving exclusive control to the current client.
- Invoking `attach` when no sessions exist will fail; tmux does **not** auto‑create a session.

---

## Minimal Working Example

Suppose you have previously created a session named `mywork` and detached from it. To re‑attach:

```sh
$ tmux attach -t mywork
````

If that is the only tmux session you have running (or it’s the most recent unattached session), you may omit `-t`:

```sh
$ tmux attach
```

To take over the session exclusively (detaching any other clients):

```sh
$ tmux attach -d -t mywork
```

---
