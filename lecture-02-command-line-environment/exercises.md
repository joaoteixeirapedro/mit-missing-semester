# MIT Missing Semester — Lecture 02

Solutions and notes for the exercises from **Lecture 02** of MIT's *The Missing Semester of Your CS Education*.

---

## Arguments and Globs

### 1. What does `--` do?

`--` is useful when a positional argument begins with `-`, because the program might otherwise interpret it as a command-line option.

It tells the program to stop parsing flags and treat everything after it as a positional argument.

For example:

```bash
rm -- -myfile
```

This removes a file named `-myfile` without interpreting its name as a command-line option.

---

### 2. Useful `ls` flags

```bash
ls -alht --color=auto
```

Flags used:

- `-a` — includes all files, including hidden files such as `.bashrc`, `.` and `..`
- `-l` — uses the long listing format, showing permissions, owner, size, modification date, etc.
- `-h` — displays file sizes in a human-readable format, such as `1.1M`, `106M`, or `1.5K`
- `-t` — sorts files by modification time, with the newest files first
- `--color=auto` — colorizes the output when it is displayed in a terminal

---

### 3. `printenv` vs `export`

`printenv` displays environment variables available to processes using the following format:

```text
NAME=value
```

`export`, when used without arguments in Bash, displays exported shell variables using Bash syntax, for example:

```bash
declare -x NAME="value"
```

---

## Environment Variables

### 1. `marco` and `polo`

```bash
marco() {
    MARCO_DIR="$PWD"
}

polo() {
    cd "$MARCO_DIR"
}
```

`marco` stores the current working directory in the `MARCO_DIR` variable.

`polo` changes the current directory back to the directory previously stored by `marco`.

---

## Return Codes

### 1. Run a script until it fails

```bash
count=0

while true; do
    count=$((count + 1))

    if ! ./random.sh > stdout.txt 2> stderr.txt; then
        echo "Failed after $count runs"
        cat stdout.txt
        cat stderr.txt
        break
    fi
done
```

The loop repeatedly executes `random.sh`.

Standard output and standard error are redirected to separate files:

```text
stdout.txt
stderr.txt
```

When the script returns a non-zero exit status, the loop stops and displays how many executions occurred before the failure, followed by the captured output.

---

## Signals and Job Control

### 1. Suspend, resume, find, and terminate a process

Start a long-running process:

```bash
sleep 10000
```

Suspend it with:

```text
Ctrl+Z
```

Resume it in the background:

```bash
bg
```

Find the process:

```bash
pgrep -lf "sleep 10000"
```

Terminate it:

```bash
pkill -f "^sleep 10000$"
```

---

### 2. `pidwait`

```bash
pidwait() {
    while kill -0 "$1" 2>/dev/null; do
        sleep 1
    done
}
```

The function checks whether a process with the supplied PID still exists.

`kill -0` does not send a signal to the process. Instead, it checks whether the process exists and whether the current user has permission to signal it.

The loop continues until the process terminates.

---

## Files and Permissions

### 1. Find files by modification time

Find the most recently modified file:

```bash
find . -type f -printf '%T@ %p\n' | sort -nr | head -n 1
```

List all files from newest to oldest:

```bash
find . -type f -printf '%T@ %p\n' | sort -nr
```

Here:

- `find . -type f` finds regular files recursively from the current directory
- `%T@` prints the modification timestamp
- `%p` prints the file path
- `sort -nr` sorts numerically in reverse order
- `head -n 1` keeps only the newest result

---

## Terminal Multiplexers

### 1. `.tmux.conf`

```tmux
# Change prefix from Ctrl+B to Ctrl+A
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Easier pane splitting
bind | split-window -h
bind - split-window -v

# Reload configuration with prefix + r
bind r source-file ~/.tmux.conf

# Switch panes using Alt + arrow keys
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Enable mouse support
set -g mouse on

# Don't automatically rename windows
set-option -g allow-rename off
```

---

## Aliases and Dotfiles

### 1. Dotfiles repository

My dotfiles are available here:

[github.com/joaoteixeirapedro/dotfiles](https://github.com/joaoteixeirapedro/dotfiles)

---

## Remote Machines (SSH)

### 6. Mosh connection recovery

Yes.

Mosh was able to recover after the VM temporarily lost its network connection.

The session became unresponsive while the network was disconnected, but after reconnecting the network adapter, the same Mosh session resumed without requiring a manual reconnection.

---

### 7. Background SSH port forwarding

```bash
ssh -N -f vm
```

The flags are:

- `-N` — tells SSH not to execute a remote command or open a remote shell. This is useful when SSH is being used only for port forwarding.
- `-f` — tells SSH to move to the background after authentication.

Since the SSH configuration already contains the required `LocalForward` configuration, running:

```bash
ssh -N -f vm
```

creates the SSH tunnel in the background.
