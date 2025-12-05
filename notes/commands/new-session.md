# `new-session`

## Overview

The `new-session` command in tmux serves to create a fresh *session*; a distinct, independent workspace managed by the tmux server. In the tmux architecture, a *session* is a top-level container that holds *windows*, each window may hold one or more *panes*, and clients (terminal windows) may attach to a session to interact with its windows and panes.

Using `new-session` allocates a new session resource (with its own windows/panes), optionally runs a command or shell in that context, and (unless instructed otherwise) attaches a client to that session. Thus, the command manipulates the workspace resources; it does not reconfigure tmux behavior globally, but instantiates a new runtime context for the user’s shell(s).

---

## Canonical Syntax Pattern

```bash
tmux new-session [ -AdDEPX ] [ -c start-directory ] [ -e VAR=value ] [ -f flags ] [ -F format ] 
                  [ -n window-name ] [ -s session-name ] [ -t group-name ] [ -x width ] [ -y height ] 
                  [ shell-command [argument ...] ]
```

Abbreviated alias: `tmux new`

### Flags and parameters

* `-s session-name`; assign a custom name to the new session. If omitted, tmux assigns a numeric name (0, 1, 2, …).
* `-n window-name`; set the name of the initial window in the new session.
* `-d`; create the new session **detached**, i.e. do not attach the client immediately.
* `-A`; if a session with the given name exists, attach to it; otherwise, create a new session.
* `-D`; used in combination with `-A`: attach (or create) and also detach any other clients attached to that session.
* `-x width`, `-y height`; specify width and height of the initial window (useful when detached, or when you want a non-default size).
* `-c start-directory`; set the starting directory for the session's initial shell/command.
* `-e VAR=value`; set environment variable(s) for the new session. May be specified multiple times.
* `-f flags`, `-F format`; for passing client-flags or specifying custom format for printing session info (advanced usage).
* `shell-command [argument ...]`; optional: instead of launching a default shell in the initial window, run the specified command (single argument passed to shell, or multiple arguments to run directly).

If no command is given (`tmux new-session` with no arguments), tmux will launch the default shell in a new session with one window and one pane.

---

## Typical Use Cases

* A user wants to start a fresh terminal multiplexer workspace; e.g., when beginning a new project or remote SSH work.
* Creating a named session (via `-s`) to better organize multiple simultaneous tmux workspaces (e.g., “backend”, “logs”, “deploy”).
* Running a specific command or program in a self-contained window managed by tmux (e.g., `tmux new-session -s build 'make && bash'`).
* Starting a tmux session detached (`-d`), e.g., when launching background long-running processes on a server, then attaching later.
* Ensuring idempotent session creation or attachment via the `-A` flag; useful in shell startup scripts to avoid duplicates.
* Starting a session in a specific directory (`-c`) with predefined environment (`-e`); helpful for project-scoped sessions.

---

## Behavioral Notes

* `new-session` operates at the **session** scope: it creates a new session rather than adjusting global configuration. It is a runtime workspace manipulation command, not a configuration command.
* Unless `-d` is used, the invoking client attaches to the new session immediately. Without a custom name, tmux auto-assigns numeric session names.
* If `-A` is used with an existing session name, tmux attaches to that session instead of creating a duplicate; making it safe in scripts that should “reuse-or-create” sessions.
* The initial window created is index 0, with a single pane by default. If you close that pane/window (e.g. by `exit`), tmux terminates the session (if no windows remain).
* `-x` and `-y` flags let you override default window size; this matters for detached sessions or when you want consistent pane dimensions; otherwise tmux uses global defaults or derives from current client.

---

## Minimal Working Example

```bash
# Create a new named session called "work"
tmux new-session -s work
```

This launches tmux (if not already running), creates session “work” containing one window (index 0) with a shell in a single pane, and attaches your terminal to it; ready for immediate use.

---
