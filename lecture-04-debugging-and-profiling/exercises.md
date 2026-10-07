# Debugging and Profiling

## Debugging

### 1. Debugging Merge Sort

I implemented the merge sort algorithm in Python and used the debugger to step through the `merge()` function.

While inspecting `left`, `right`, `i`, `j`, and `result`, I noticed that the problem occurred inside the `else` block:

```python
else:
    result.append(right[i])
    j += 1
```

The algorithm was selecting an element from `right`, but it was using `i`, which is the index for the `left` list.

The correct index is `j`.

#### Fix

```python
else:
    result.append(right[j])
    j += 1
```

#### Correct implementation

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    return merge(left, right)


def merge(left, right):
    result = []
    i = 0
    j = 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    result.extend(left[i:])
    result.extend(right[j:])

    return result


print(merge_sort([3, 1, 4, 1, 5, 9, 2, 6]))
```

Output:

```text
[1, 1, 2, 3, 4, 5, 6, 9]
```

The debugger helped identify the bug when `i` and `j` had different values, making it clear that the wrong index was being used to access the `right` list.

---

### 2. Reverse Debugging with `rr`

I compiled the program with debug information:

```bash
gcc -g corruption.c -o corruption
```

Running the program showed that `students[1].id` changed from `1002` to `1007` after calling:

```c
curve_scores(0, 5);
```

I recorded and replayed the execution using:

```bash
rr record ./corruption
rr replay
```

During the replay, I stopped after the corruption and created a watchpoint:

```gdb
watch students[1].id
reverse-continue
```

Reverse execution stopped at:

```c
students[student_idx].scores[i] += curve;
```

Inspecting the variables showed:

```text
student_idx = 0
i = 3
```

However, `scores` is declared as:

```c
int scores[3];
```

so its valid indexes are only:

```text
0
1
2
```

I also checked the memory addresses:

```gdb
print &students[0].scores[3]
print &students[1].id
```

Both expressions pointed to the same memory location.

Therefore, writing to:

```c
students[0].scores[3]
```

overwrote:

```c
students[1].id
```

The bug was:

```c
for (int i = 0; i < 4; i++)
```

The fix is:

```c
for (int i = 0; i < 3; i++)
```

After fixing the loop, `students[1].id` remains `1002`.

---

### 3. AddressSanitizer — Use-After-Free

I first compiled and ran the program without sanitizers:

```bash
gcc uaf.c -o uaf
./uaf
```

The behavior was unpredictable because the program accesses memory after it has been freed.

I then compiled it with AddressSanitizer:

```bash
gcc -fsanitize=address -g uaf.c -o uaf
./uaf
```

AddressSanitizer reported a:

```text
heap-use-after-free
```

The problem occurs here:

```c
free(greeting);

greeting[0] = 'J';
```

After `free(greeting)`, the pointer still contains an address, but that memory no longer belongs to the program. Accessing it is undefined behavior.

The fix is to modify the string before freeing it:

```c
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

int main() {
    char *greeting = malloc(32);

    strcpy(greeting, "Hello, world!");
    printf("%s\n", greeting);

    greeting[0] = 'J';
    printf("%s\n", greeting);

    free(greeting);

    return 0;
}
```

The corrected program outputs:

```text
Hello, world!
Jello, world!
```

---

### 4. Tracing System Calls with `strace`

I traced `ls -l` with:

```bash
strace ls -l
```

For a more readable list of file-related operations, I also used:

```bash
strace -e trace=file ls -l
```

Some of the system calls I observed included:

```text
execve()
openat()
read()
write()
close()
mmap()
newfstatat()
getdents64()
```

Examples of what they do:

- `execve()` starts the program.
- `openat()` opens files and shared libraries.
- `read()` reads data.
- `write()` writes output.
- `close()` closes file descriptors.
- `mmap()` maps files or memory into the process address space.
- `newfstatat()` obtains file metadata.
- `getdents64()` reads directory entries.

I also tried tracing another program:

```bash
strace -e trace=openat python3 example.py
```

This made it easier to see libraries, modules, configuration files, and other files opened while the program was running.

---

### 5. Using an LLM for Debugging

I tested using an LLM to interpret debugging output from tools such as AddressSanitizer and `strace`.

For example, I provided the AddressSanitizer error:

```text
ERROR: AddressSanitizer: heap-use-after-free
```

The explanation helped identify that the program was accessing `greeting` after calling:

```c
free(greeting);
```

The LLM was useful for translating a large diagnostic report into:

1. the type of error;
2. where it occurred;
3. why it occurred;
4. a possible fix.

However, I still verified the explanation against the source code and debugging output rather than assuming the generated answer was correct.

---

# Profiling

## 1. Basic Profiling with `perf stat`

I ran:

```bash
perf stat ./slow
```

`perf stat` reports hardware and operating-system performance counters.

Some important counters are:

- **task-clock** — CPU time used by the program.
- **context-switches** — how often execution switched between processes or threads.
- **cpu-migrations** — how often the process moved between CPU cores.
- **page-faults** — accesses that required the OS to resolve a virtual-memory mapping.
- **cycles** — CPU clock cycles spent executing the program.
- **instructions** — number of CPU instructions executed.
- **branches** — branch instructions executed.
- **branch-misses** — branches incorrectly predicted by the CPU.

A useful metric is IPC:

```text
instructions / cycles
```

It represents approximately how many instructions are completed per CPU cycle.

---

## 2. Profiling `slow.c` with `perf record`

I compiled the program with optimization and debug symbols:

```bash
gcc -g -O2 slow.c -o slow -lm
```

Then recorded profiling samples:

```bash
perf record -g ./slow
```

and inspected them with:

```bash
perf report
```

The profile showed that most of the execution time was spent inside the repeated mathematical computation, particularly around:

```c
sin(i * j)
cos(i + j)
```

inside:

```c
slow_computation()
```

This makes sense because the function executes nested loops and performs expensive trigonometric operations many times.

### Flame Graph

Using Brendan Gregg's FlameGraph scripts, the profiling data can be converted with commands similar to:

```bash
perf script > out.perf
stackcollapse-perf.pl out.perf > out.folded
flamegraph.pl out.folded > flamegraph.svg
```

The flame graph provides a visual representation of where the program spends most of its CPU time.

---

## 3. Benchmarking with `hyperfine`

I compared `grep` and `ripgrep` using:

```bash
hyperfine \
    'grep -r "TODO" .' \
    'rg "TODO" .'
```

`hyperfine` runs each command multiple times and reports statistics such as:

```text
mean
min
max
standard deviation
```

This is more reliable than manually running:

```bash
time command
```

only once.

The benchmark showed which implementation completed the same search task faster on my system.

---

## 4. `htop`, `taskset`, and CPU Affinity

I monitored processes with:

```bash
htop
```

Then I limited a CPU-intensive program to CPUs `0` and `2`:

```bash
taskset --cpu-list 0,2 stress -c 3
```

`stress -c 3` creates three CPU workers.

However:

```text
taskset --cpu-list 0,2
```

allows the process to execute only on **two CPUs**.

Therefore, three workers exist, but only two of them can execute simultaneously. The operating system scheduler must share those two CPUs between the three workers.

This demonstrates the difference between:

```text
number of workers
```

and:

```text
number of CPUs available to those workers
```

---

## 5. Finding Which Process Is Using a Port

I started a web server on port `4444`:

```bash
python3 -m http.server 4444
```

In another terminal, I searched for the process listening on that port:

```bash
ss -tlnp | grep 4444
```

The output identifies the process and its PID.

I then terminated it with:

```bash
kill <PID>
```

After running:

```bash
ss -tlnp | grep 4444
```

again, there was no listener on the port.

This is useful when an application fails with an error such as:

```text
Address already in use
```

because it allows me to identify exactly which process owns the port.
