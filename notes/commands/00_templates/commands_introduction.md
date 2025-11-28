# `<command name>` (e.g. `set`)

## Overview

Provide a concise, high-level description of the command’s conceptual purpose within the tmux architecture. Emphasize operational intent rather than implementation detail.

Explain how the command interacts with tmux’s core abstractions (server, session, window, pane, client). Clarify whether the command primarily configures behavior, manipulates workspace resources, or directs user input.

---

## Canonical Syntax Pattern

Present the generic form of the command without enumerating flags. Illustrate structural syntax (e.g., `command [target] [parameters]`). Avoid operational minutiae; focus on shape and usage expectations.

---

## Typical Use Cases

Enumerate the standard introductory scenarios in which a novice user would apply the command. Prioritize pedagogical clarity over exhaustiveness.

---

## Behavioral Notes

Highlight key characteristics that influence how a learner should think about the command. Example considerations:

* Whether the command affects persistent configuration vs. ephemeral runtime state
* Whether the command operates globally or within a specific scope
* Any ordering or evaluation rules that matter for beginners

---

## Interaction With Other tmux Constructs

Identify important relationships with other commands or features. Provide minimal conceptual cross-references to help contextual navigation through the configuration space.

---

## Minimal Working Example

Provide a single, small, didactic example illustrating usage without deep parameterization. Keep the example focused strictly on syntax comprehension.

---
