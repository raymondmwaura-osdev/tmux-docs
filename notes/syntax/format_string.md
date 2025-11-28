# Format Strings

## What are “formats” in tmux

* In tmux, a *format* is a string template containing **directives** of the form `#{…}`. Inside the braces one references variables, options or expressions.
* tmux evaluates those directives at runtime, substituting variable values or the result of expressions.
* Formats are used in many contexts: status‑lines, window/pane titles, `display-message`, outputs of list commands (`list-windows`, `list-panes`, etc.), automatic rename, conditional configuration, and more.

Example:

```bash
tmux display -p '#{session_name}'
```

This prints the name of the current session.

---

## Basic syntax

* A format directive always appears inside `#{` and `}`. For example `#{window_name}`, `#{pane_current_path}`.
* Some short aliases exist (legacy), e.g. `#S` for `#{session_name}`. Use of these aliases is allowed but *not encouraged*, especially if you want modifiers.
* If you need a literal `#`, or literal `}`, you must escape: `##` becomes `#`, `#}` becomes `}` etc.
* Besides built-in variables, you can reference user-defined tmux-options (e.g. `@foo`) or even environment variables (global).

---

## Variables you can refer to

Variables expose information about the tmux environment. Which variables are available depends on context (server, session, window, pane):

* **Server-wide**: e.g. `host`, `host_short`, `socket_path`, `start_time`, `pid`.
* **Session‑specific**: e.g. `session_name`, `session_id`, `session_windows`, `session_height`, `session_width`, `session_activity`, `session_created`, plus server‑wide variables.
* **Window‑specific**: e.g. `window_name`, `window_index`, `window_panes`, `window_layout`, `window_width`, `window_height`, `window_active`, `window_activity`, `window_id`, plus session & server variables.
* **Pane‑specific**: e.g. `pane_id`, `pane_index`, `pane_active`, `pane_current_path`, `pane_current_command`, `pane_pid`, `pane_width`, `pane_height`, `cursor_x`, `cursor_y`, plus window, session, server variables.

Because of this hierarchical model, when you use a format in a context that lists panes (or windows), you can access both local (pane/window) and global (session/server) variables.

---

## Format modifiers

Beyond simply inserting variable values, tmux provides a rich set of **modifiers** to transform or conditionally format those values. The general form is: inside `#{…}`, you prefix the modifier, then a colon, then the variable (or sub-format). Multiple modifiers can be chained (separated by `;`).

Here are key categories:

### String/path transformations

* `b:`; **basename**: for a path, returns only the filename part (like `basename(3)`).
* `d:`; **dirname**: for a path, returns only the directory portion (like `dirname(3)`).
* `q:`; **quote / escape**: escapes characters that matter for shell usage (special chars, like `#`, etc.), to make string safe in shell context.

### Trimming and padding

* `=N:`; **trim to width N**: if the variable expands to a string longer than N, it is trimmed. Positive N keeps from start, negative N from end.
* `pN:`; **pad to width N**: pads the string to width N. Positive N pads on the left, negative N on the right.
* When using `=`, an optional second argument may be provided: a string to append (or prepend) to show the trimming, e.g. an ellipsis `...`. The syntax uses a delimiter (`|` or `/` etc.) between arguments.

**Example**:

```
#{=|5|...:pane_title}
```

This trims the pane title to max 5 chars; if trimmed, appends `...`.

You can chain padding and trimming. For example:

```
#{=3;p-6:@v}
```

First trims to 3 characters, then pads (right) to width 6.

---

## Conditionals and comparisons

tmux formats support logic: you can compare, match, choose between strings, do boolean logic, etc. This enables building dynamic output.

* **Comparison operators**: `==`, `!=`, `<`, `>`, `<=`, `>=` — perform string comparisons between two formats or variables.
* **Logical operators**: `||` (OR), `&&` (AND) — combine boolean expressions.
* **Ternary / choice operator `?`**: syntax `#{?cond,then_format,else_format}`. If `cond` evaluates true (non-empty and not “0”), result is `then_format`, else `else_format`.

Because conditionals and modifiers can be nested, you can build complex logic. For example, nested `?`, combined with comparisons.

Important detail: within conditionals, commas `,` and `}` have syntactic significance. To include literal `,` or `}`, you must escape them as `#,` or `#}` respectively; unless part of a `#{…}` expansion.

---

## Substitution, matching, searching

tmux formats also offer text substitution, pattern matching, and searching inside pane content.

* **Substitution; the `s` modifier**: similar to `sed`. Syntax: `s/regex/replacement/flags:`. Applies to a variable (not full format). You can supply regular expressions; the replacement can reference capture groups (`\1`, `\2`, …). Flags: `i` for case-insensitive. Multiple substitutions can be chained with `;`.

  Example:

  ```
  #{s|foo|bar|:@v}
  ```

  replaces occurrences of “foo” with “bar” in variable `@v`.

* **Pattern matching; the `m` modifier**: tests whether a pattern matches a string. First argument: pattern, second: string (or format). Optional flags: `r` (regular expression), `i` (ignore case). Returns boolean (true/false represented by 1 or empty).

* **Content search; the `C` modifier**: searches through the content of the pane (text inside terminal) for a given pattern; returns the matching line number or 0 if not found. Supports same flags as `m` (e.g. `r`, `i`). Useful for scripting status lines based on whether a string appears in pane.

---

## Numeric expressions (arithmetic)

Since tmux 3.2 (and some recent releases), numeric operations can be done via the `e` modifier.

* Operators supported: `+` (add), `-` (subtract), `*` (multiply), `/` (divide), `m` or `%` (modulus).
* Optionally one can force floating‑point arithmetic by specifying a flag (`f`), and indicate number of decimal places. If no flag, integer arithmetic is used.

Example:

```bash
tmux display -p '#{e|+|:1,1}'
```

Yields `2`.
Or:

```bash
tmux display -p '#{e|/|f|4:10,3}'
```

Yields `3.3333`.

This enables doing on-the-fly calculations in status‑lines or scripts executed via tmux formats.

---

## Looping; iterating across sessions/windows/panes

tmux allows you to loop over collections (sessions, windows, panes) and apply a format for each, via modifiers:

* `S:`; iterate over **all sessions**.
* `W:`; iterate over **all windows** in current session.
* `P:`; iterate over **all panes** in current window.

Within the loop, the format is expanded once per element. For windows and panes, you can supply *two* comma‑separated formats: the first for non-active windows/panes, the second for the active one.

Example: listing window names in a status‑style list:

```
#{W:#{window_name} ,--- }
```

This yields a space-separated list of window names, inserting `,--- ` between them.

Another example: use different format for current window:

```
#{W:#{E:window-status-format} ,#{E:window-status-current-format}}
```

This standard idiom allows building dynamic status bars showing window lists.

---

## Double-expansion, time-specific, and literal

* `E:`; **double expansion**: expands the result twice. Useful when a variable or option itself contains a format-string. For example, expanding an option whose value is itself a format.
* `T:`; like `E:`, but additionally expands any time‑format specifiers (similar to `strftime(3)`). Useful for embedding date/time in status line, window names, etc.
* A literal string insertion: via `l:`; ensures the content is treated literally (i.e. not further expanded). This can be used when embedding conditional expressions or format syntax as literal text.

---

## Shell commands output insertion

tmux allows embedding the output of external shell commands in a format via `#(...)`. The output (usually the last line) of the command is inserted. This is quite powerful for dynamically injecting external information (system status, git branch, load average, etc.) into status lines.

**Important nuance**: tmux does not wait indefinitely for the command to finish; if it's still running, it uses the most recent line from previous invocation; if never run, a placeholder may be used. The status line is generally updated at most once per second.

Thus, using `#(...)` in a frequently updated status line is safe but you should design commands to run quickly or periodically.

---

## Example use cases

### Example 1; custom status line with current window/pane info

In your `~/.tmux.conf`, you might set:

```tmux
set -g status-right '#{session_name} | #S:#I.#P #{pane_current_path}'
```

This will show session name, window and pane index, current working directory; dynamically updated.

### Example 2; trimmed pane title

```tmux
set -g pane-border-format '#{=-15:#{pane_title}}'
```

If `pane_title` is longer than 15 characters, it will be truncated (from the left or right, depending on sign).

### Example 3; conditional indicator

```tmux
set -g status-left '#{?pane_in_mode,#[reverse] MODE ,}'
```

If the current pane is in “mode” (e.g. copy mode), displays “MODE” reversed; otherwise nothing.

### Example 4; list windows in a status bar

```tmux
set -g status-format[0] "#{W:#{window_index}:#{window_name} , }"
```

This loops over every window, outputting its index and name, separated by commas.

### Example 5; embed system load via shell

```tmux
set -g status-right '#(uptime) | %H:%M'
```

This will run `uptime`, capture its output, and insert into status bar along with time (assuming time formatting is configured).

---

## Common pitfalls and best practices

* **Escape special characters**: Because `#`, `,`, `}` have syntactic meaning, any literal occurrence must be escaped (e.g. `##`, `#,`, `#}`). Failure leads to incorrect parsing.
* **Use full variable names rather than short aliases** when you intend to apply modifiers; many modifiers do not work with short alias syntax.
* **Be careful with shell-command inserts (`#(...)`)**: commands should be efficient; avoid long-running commands inside frequently updated status lines.
* **Complex nested formats can become unreadable**; when building heavy logic (conditionals, loops, substitutions), consider external scripts or breaking into smaller parts.
* **Test using `display-message -p` first**: this lets you debug format strings easily. For instance:

  ```bash
  tmux display -p "#{session_name} #{window_name} #{pane_current_path}"
  ```

  If something fails, output will often be empty or missing part of the string.

---
