# Lecture 03 — Editors and IDEs

## Exercise 1 — Vim Mode

I enabled Vim-style editing in my development environment and started practicing the basic Vim workflow.

Some of the commands I practiced include:

- `i` — enter Insert mode
- `Esc` — return to Normal mode
- `h`, `j`, `k`, `l` — move the cursor
- `w` — move to the beginning of the next word
- `b` — move to the beginning of the previous word
- `dd` — delete a line
- `yy` — copy a line
- `p` — paste
- `/` — search
- `A` — move to the end of the line and enter Insert mode

The main goal of this exercise was to become more comfortable with modal editing and to start looking for more efficient ways to perform repetitive editing tasks.

---

## Exercise 2 — VimGolf

I completed the **Word Completion** challenge on VimGolf:

`https://www.vimgolf.com/challenges/9v0066daede50000000003a8`

The challenge required expanding abbreviated Vim configuration options into their complete forms.

For example:

```text
set noco
```

had to become:

```text
set nocompatible
```

One of the most useful commands I learned while solving the challenge was:

```text
Ctrl-X Ctrl-V
```

When used in Insert mode, it provides completion for Vim commands and options.

I also practiced using macros to avoid repeating the same editing operation manually.

For example:

```text
qq
A
Ctrl-X Ctrl-V
Esc
j
q
```

This records a macro in register `q` that:

1. Moves to the end of the current line.
2. Enters Insert mode.
3. Completes the Vim option.
4. Returns to Normal mode.
5. Moves to the next line.

The macro can then be repeated with:

```text
13@q
```

This exercise showed how Vim commands, completion, macros, and repetition can make repetitive text editing much more efficient.

---

## Exercise 3 — IDE Extension and Language Server

I configured Visual Studio Code for Python development using the official **Python** extension and **Pylance** as the language server.

I tested features such as:

- Code completion
- Hover documentation
- Error detection
- Go to Definition
- Symbol navigation
- Library dependency navigation

For example, I used Python's `pathlib` module:

```python
from pathlib import Path

path = Path("test.txt")

print(path.exists())
```

I verified that VS Code could provide autocomplete for methods such as `exists()` and navigate to definitions using features such as **Go to Definition**.

This exercise helped me understand the role of a language server in an IDE. The editor itself provides the interface, while the language server analyzes the code and enables features such as autocomplete, type information, navigation, and diagnostics.

---

## Exercise 4 — IDE Extension

I browsed the Visual Studio Code extension marketplace and chose **GitLens** as an additional extension.

GitLens extends the Git functionality available inside VS Code and provides features such as:

- File history
- Commit information
- Line authorship
- Repository history
- Comparison between revisions
- Easier inspection of Git changes

Since I am already using Git and GitHub throughout the Missing Semester exercises, GitLens is particularly useful for understanding how the history of a project evolves and for working with repositories directly from the editor.
