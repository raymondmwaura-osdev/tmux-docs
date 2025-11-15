# Introduction to tmux Sessions

In the context of tmux, a **session** is a persistent container for one or more terminal *windows* (and within them, *panes*) that remain alive even when the user detaches from or disconnects the terminal. The session lives on the tmux server process and can be re-attached later.

Using sessions allows you to:

* Launch long-running tasks (e.g., builds, servers) and detach without terminating them.
* Maintain multiple independent workspaces (e.g., “development”, “logs”, “backup”) within the same terminal multiplexer environment.
* Reattach from different terminals or locations to the same session.

---

## Creating a Session

**Command-line method**

* To create a new session with a default name (typically “0”), simply run:

  ```
  tmux
  ```

  This launches a tmux server (if not already running) and attaches you to a new session.
* To create a new session **with** a specific name, use:

  ```
  tmux new -s session_name
  ```

  or equivalently:

  ```
  tmux new-session -s session_name
  ```

  By naming the session you make it easier to manage in multi-session environments.

**Inside tmux**
If you are already inside a tmux session and wish to create another session, you can use the tmux command prompt (prefix + :) with `new-session -s …`.

---

## Listing Active Sessions

To see all currently running sessions:

```
tmux ls
```

or

```
tmux list-sessions
```

This will display each session’s name, its identifier, how many windows it has, etc.

---

## Attaching and Detaching Sessions

**Detach**
If you are inside a tmux session and you wish to leave (i.e., disconnect but keep the session running), press the prefix (default `Ctrl+b`), then `d`. This detaches your client but leaves the session and its processes alive. Alternatively, you can type `detach` in the tmux command prompt (prefix + :).

**Attach**
To re-attach to an existing session, use:

```
tmux attach
```

which attaches to the most recently used session, or specify a particular session:

```
tmux attach -t session_name
```

or shorthand:

```
tmux a -t session_name
```

---

## Switching Between Sessions

When you have multiple sessions running, you may wish to switch from one session to another. Two common methods:

* From the shell:

  ```
  tmux switch -t session_name
  ```

  This instructs tmux to switch the current client to the target session.  
  You can also type `switch -t session_name` in the tmux command prompt.
* From inside tmux: Press the prefix (`Ctrl+b`), then `s`. This brings up an interactive list of sessions; you can choose one to switch into.

---

## Renaming a Session

Renaming helps keep track of which session is for which task. You can rename a session either from inside tmux or from the shell.

**From the shell:**

```
tmux rename-session -t old_name new_name
```

If you omit the `-t old_name` and you are already in the session you want to rename, you can simply do:

```
tmux rename-session new_name
```

**From inside tmux:**

* Press the prefix (`Ctrl+b`), then `$`. A prompt appears to enter the new session name.
* Or, prefix + `:` to open the tmux command prompt, then type:

  ```
  rename-session new_name
  ```

  (Or with `-t old_name` if renaming a different session.)

---

## Terminating (Killing) a Session

To end a session and thereby terminate all its windows/panes and processes:

```
tmux kill-session -t session_name
```

Or if you are in the session and want to kill it:

```
tmux kill-session
```

You can also kill all sessions except the one you are in:

```
tmux kill-session -a
```

---
