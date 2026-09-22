# Lecture 1 — Course Overview + Introduction to the Shell

## 1

`echo $SHELL` returned:

```bash
/bin/bash
```

I am using the correct Unix shell.

## 2

The `-l` flag makes `ls` display detailed information about files and directories.

The first 10 characters describe the file type and permissions. The first character represents the file type, and the next nine represent permissions for the owner, group, and others.

## 3

A glob is a pattern used by the shell to match file names.

Examples:

```bash
ls *.txt
ls file?.txt
ls {a,b,c}.txt
```

## 4

Single quotes preserve everything literally. Variables, `!`, backslashes, and other special characters are not expanded.

Double quotes preserve spaces and most characters, but still allow things like variable expansion (`$USER`) and command substitution (`$(...)`).

ANSI-C quotes (`$'...'`) interpret escape sequences such as `\n`, `\t`, and `\\`.

```bash
echo $'test $ and !\nnew line'
```

## 5

Redirect stdout and stderr to separate files:

```bash
ls /nonexistent /tmp > stdout.txt 2> stderr.txt
```

Redirect both to the same file:

```bash
ls /nonexistent /tmp > output.txt 2>&1
```

## 6

```bash
[ -d /tmp/mydir ] || mkdir /tmp/mydir
```

## 7

`cd` needs to be built into the shell because it has to change the working directory of the shell itself.

If it were an external program, it would run in a child process and would only be able to change the working directory of that child process, not the parent shell.

## 8

```bash
#!/bin/bash

if [ -f "$1" ]; then
    echo "The file exists"
else
    echo "The file does not exist"
fi
```

## 9

Before adding execute permission, running the script with:

```bash
./check.sh somefile
```

resulted in `Permission denied`.

After running:

```bash
chmod +x check.sh
```

the script could be executed directly.

This step is necessary because the file needs execute permission before it can be run directly.

## 10

`set -x` makes the shell print each command as it is executed. It is useful for debugging shell scripts.

## 11

```bash
cp notes.txt notes_$(date +%Y-%m-%d).txt
```

## 12

Replace the hardcoded test command with `"$@"`:

```bash
while "$@" > "$LOGFILE" 2>&1; do
```

This allows the script to receive the command and its arguments when it is executed.

## 13

```bash
find ~ -type f -printf '%f\n' | sed -n 's/.*\.//p' | sort | uniq -c | sort -nr | head -5
```

## 14

```bash
find . -type f -name "*.sh" | xargs wc -l
```

Bonus — handle filenames containing spaces:

```bash
find . -type f -name "*.sh" -print0 | xargs -0 wc -l
```

## 15

```bash
curl -s https://missing.csail.mit.edu/ | grep -oE 'href="/2026/[^"]+/"' | sort -u | wc -l
```

Output:

```text
9
```

## 16

```bash
curl -s https://microsoftedge.github.io/Demos/json-dummy-data/64KB.json | jq -r '.[] | select(.version > 6) | .name'
```

## 17

```bash
printf 'a 50 x\nb 150 y\nc 200 z\n' | awk '$2 > 100 {print $3, $2, $1}'
```

Output:

```text
y 150 b
z 200 c
```

## 18

The SSH log pipeline works in several steps:

- `ssh` connects to the remote server and runs the log command there.
- `journalctl` retrieves SSH server logs from the previous boot.
- `grep` keeps only SSH disconnection messages.
- `sed` extracts the username from each message.
- `sort | uniq -c` groups identical usernames and counts them.
- `sort` orders them by count.
- `tail` keeps the 10 most common usernames.
- `awk` extracts only the username.
- `paste` joins the usernames into a comma-separated line.

A similar pipeline for finding my most-used Bash commands is:

```bash
awk '{print $1}' ~/.bash_history | sort | uniq -c | sort -nr | head
```
