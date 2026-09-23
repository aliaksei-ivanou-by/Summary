# Linux and Shell Technical Interview Questions and Answers

> Reusable bank for the operating-system side of a C or C++ role: the filesystem, permissions, processes, signals, logs, redirection and shell scripting. Program-level systems topics - memory sections, libraries, `dlopen`, core dumps - live in [C Language Questions](<./C Language Questions.md>); process versus thread, IPC and socket mechanics live in [C++ Core Questions](<./C++ Core Questions.md>).

# Question Index

## Filesystem and Permissions (NIX-001–NIX-005)

|  |  |  |
|---|---|---|
| [NIX-001. How is a Unix filesystem laid out, and what lives where?](#question-nix-001) | [NIX-002. How do Unix file permissions work?](#question-nix-002) | [NIX-003. Hard link vs symbolic link?](#question-nix-003) |
| [NIX-004. How do you find things - `find`, `grep`, `locate`?](#question-nix-004) | [NIX-005. What does `sudo` actually do, and why `visudo`?](#question-nix-005) |  |

## Processes and Signals (NIX-006–NIX-009)

|  |  |  |
|---|---|---|
| [NIX-006. How do you inspect what is running?](#question-nix-006) | [NIX-007. What does `kill` do, and why is `kill -9` a last resort?](#question-nix-007) | [NIX-008. A process is hung - how do you find out what it is waiting on?](#question-nix-008) |
| [NIX-009. How do you manage a service with systemd?](#question-nix-009) |  |  |

## Shell (NIX-010–NIX-014)

|  |  |  |
|---|---|---|
| [NIX-010. How do redirection and pipes work?](#question-nix-010) | [NIX-011. What is a shebang, and why `#!/usr/bin/env bash`?](#question-nix-011) | [NIX-012. Which shell startup files run when?](#question-nix-012) |
| [NIX-013. What makes a shell script safe rather than merely working?](#question-nix-013) | [NIX-014. Where do you look when a service is misbehaving in production?](#question-nix-014) |  |

# 1. Filesystem and Permissions

## Question NIX-001

[↑ Back to question index](#question-index)

### Question NIX-001 — How is a Unix filesystem laid out, and what lives where?

**Short answer**

- One tree from `/`, with no drive letters: everything, including devices and kernel state, is a path.
- The ones that come up daily: `/bin` and `/usr/bin` for programs, `/etc` for configuration, `/home` for users, `/lib` and `/usr/lib` for shared libraries, `/var` for changing data including `/var/log`, `/tmp` for scratch, `/opt` and `/usr/local` for software outside the package manager.
- `/proc` and `/sys` are not real files: they are kernel interfaces presented as a filesystem, which is why `cat /proc/<pid>/maps` answers questions no tool needs to exist for.

**Details and nuances**

For a developer the distinction that matters most is `/usr/local` and `/opt` versus the package-managed directories: installing into `/usr/bin` by hand puts you in conflict with the package manager, and that conflict surfaces later as a confusing version mismatch.

`/proc/<pid>/` is worth knowing by name in an interview: `maps` for the address space, `fd/` for the open file descriptors, `status` for state and memory, `cmdline` for how the process was started. Those four answer most "what is this process doing" questions without any tool at all.

[↑ Back to question index](#question-index)

---

## Question NIX-002

[↑ Back to question index](#question-index)

### Question NIX-002 — How do Unix file permissions work?

**Short answer**

- Three classes - user, group, other - each with read, write and execute, shown as `rwxr-xr--` and written octally as read 4, write 2, execute 1.
- On a **directory** the bits mean something different: read lists names, write creates and deletes entries, and execute is what allows traversing into it - so `r` without `x` lets you see the names and nothing else.
- Change them with `chmod` (symbolic `u+x` or octal `755`) and ownership with `chown user:group`; `umask` decides the defaults for newly created files.

**Details and nuances**

The directory-versus-file distinction is the part interviewers probe, and the delete rule follows from it: you can delete a file you cannot write, because deletion is a write to the *directory*, not to the file. That is what the sticky bit on `/tmp` exists to prevent - with it set, only the owner can remove their own entries.

Common values worth knowing on sight: `755` for a program or a directory, `644` for an ordinary file, `600` for a secret, and `400` for a private key - which SSH will refuse to use if it is more permissive than that.

[↑ Back to question index](#question-index)

---

## Question NIX-003

[↑ Back to question index](#question-index)

### Question NIX-003 — Hard link vs symbolic link?

**Short answer**

- A hard link is another directory entry pointing at the same inode, so it is indistinguishable from the original; the data disappears only when the last link and the last open descriptor are gone.
- A symbolic link stores a path, so it breaks if the target moves or is deleted, and it can cross filesystems and point at directories - neither of which a hard link can do.
- `ln target name` makes a hard link; `ln -s target name` makes a symlink.

**Details and nuances**

The consequence people meet in practice: deleting a file that a running process still has open frees no space, because the inode survives until the descriptor closes. That is why a disk fills up and `du` disagrees with `df`, and why `lsof | grep deleted` is the diagnostic.

Symlinks are how shared-library versioning works - `libfoo.so` to `libfoo.so.1` to `libfoo.so.1.2.3` - so following that chain is part of answering why the wrong version is being loaded.

[↑ Back to question index](#question-index)

---

## Question NIX-004

[↑ Back to question index](#question-index)

### Question NIX-004 — How do you find things - `find`, `grep`, `locate`?

**Short answer**

- `find` walks the tree now and filters on name, type, size, time and permissions, and can act on what it finds with `-exec` or `-delete`.
- `grep` searches inside files - `grep -rn pattern .` recursively with line numbers is the everyday form, and `-i`, `-w`, `-A`/`-B` for context.
- `locate` queries a prebuilt index, so it is instant and possibly stale; use it to find a file by name, not to answer a question about the current state.

**Details and nuances**

Two pieces of the `find` idiom are worth having ready: `-print0` with `xargs -0` so filenames containing spaces do not split, and `-exec ... +` rather than `\;` so one command handles many files instead of forking per file.

```bash
find . -name '*.log' -mtime +7 -print0 | xargs -0 rm      # older than a week
find . -type f -newer Makefile                            # changed since a build
grep -rn --include='*.c' 'malloc(' .                      # only C files
```

On a large tree, `ripgrep` or `git grep` are dramatically faster than plain `grep -r` because they respect ignore rules - worth mentioning, and worth not assuming is installed.

[↑ Back to question index](#question-index)

---

## Question NIX-005

[↑ Back to question index](#question-index)

### Question NIX-005 — What does `sudo` actually do, and why `visudo`?

**Short answer**

- `sudo` runs one command as another user, root by default, checking `/etc/sudoers` for permission and logging the attempt - which is the difference from `su`, where you become the user and nothing records what you did.
- `/etc/sudoers` is edited with `visudo` because it validates the syntax before saving; a syntax error written directly can lock everyone out of `sudo` on that machine.
- The principle underneath: elevate for one command, not for a session, so most work happens without the privilege that can destroy the machine.

**Details and nuances**

Two practical notes. `sudo` resets much of the environment, so `sudo make install` may not see the `PATH` or variables your shell has - `sudo -E` preserves the environment, and knowing that saves a confusing half hour. And redirection happens in *your* shell, not the elevated one, so `sudo echo x > /root/f` fails while `echo x | sudo tee /root/f` works.

[↑ Back to question index](#question-index)

---

# 2. Processes and Signals

## Question NIX-006

[↑ Back to question index](#question-index)

### Question NIX-006 — How do you inspect what is running?

**Short answer**

- `ps aux` for a snapshot of everything with owner, CPU, memory and command; `ps -ef` is the other common form.
- `top` or `htop` for a live view sorted by resource use; `pidof` and `pgrep` to get a PID by name.
- Beyond that: `lsof -p <pid>` for what it has open, `ss -tulpn` for what is listening, and `/proc/<pid>/status` for its state and memory.

**Details and nuances**

The state column repays knowing: `R` running, `S` sleeping on something interruptible, `D` uninterruptible sleep - almost always blocked on I/O and not killable, which is why a hung process that ignores `kill -9` is usually in `D` - `Z` zombie, meaning it has exited and its parent has not reaped it, and `T` stopped.

A zombie is not consuming resources beyond a process-table entry; the bug is in the parent that never called `wait`. Killing the zombie does nothing; killing or fixing the parent is the answer.

[↑ Back to question index](#question-index)

---

## Question NIX-007

[↑ Back to question index](#question-index)

### Question NIX-007 — What does `kill` do, and why is `kill -9` a last resort?

**Short answer**

- `kill` sends a signal, it does not by itself kill: the default `SIGTERM` (15) asks the process to shut down and can be caught, so the process closes files, flushes buffers and releases locks.
- `SIGKILL` (9) cannot be caught or ignored - the kernel removes the process immediately - so nothing is flushed, temporary files stay, and a held lock or a half-written file is left behind.
- The order is `SIGTERM`, wait a few seconds, then `SIGKILL` only if it has not exited. `killall` and `pkill` do the same by name.

**Details and nuances**

Others worth knowing: `SIGINT` (2) is Ctrl-C, `SIGHUP` (1) is conventionally "reload your configuration" for daemons, `SIGSEGV` (11) is the invalid memory access, and `SIGSTOP`/`SIGCONT` suspend and resume without terminating.

For a C or C++ answer, the handler rule matters: a signal handler may only call async-signal-safe functions, so no `printf`, no `malloc`, no locks. The standard pattern is to set a `volatile sig_atomic_t` flag and do the real work in the main loop.

And `kill -9` not working means the process is in uninterruptible sleep - blocked in the kernel on I/O - so the thing to fix is the I/O, usually a dead mount or a failing disk.

[↑ Back to question index](#question-index)

---

## Question NIX-008

[↑ Back to question index](#question-index)

### Question NIX-008 — A process is hung - how do you find out what it is waiting on?

**Short answer**

- First decide whether it is spinning or blocked: `top` shows 100% CPU for a spin, near zero for a block, and the two have completely different causes.
- Blocked: `strace -p <pid>` shows the system call it is sitting in, `cat /proc/<pid>/stack` or the `wchan` field shows where in the kernel, and `lsof -p <pid>` shows which file or socket.
- Spinning or deadlocked in user space: attach with `gdb -p <pid>` and `thread apply all bt` - the stack of every thread, which is what identifies a lock cycle.

**Details and nuances**

`thread apply all bt` is the single most useful command for a hang, because a deadlock is visible in the shape of the output: two threads, each inside a lock acquisition, each holding what the other wants. Nothing else shows that as directly.

If the process must not be disturbed, `gcore <pid>` takes a core dump of a *running* process and leaves it running, so the analysis happens on the copy.

**Example or evidence boundary**

Production experience: diagnosing native failures over SSH with `gdb` and core dumps on AWS EC2 Linux hosts, and cross-layer debugging on embedded targets with application, SIP and system logs.

[↑ Back to question index](#question-index)

---

## Question NIX-009

[↑ Back to question index](#question-index)

### Question NIX-009 — How do you manage a service with systemd?

**Short answer**

- `systemctl start|stop|restart|status <unit>` controls it now; `enable` and `disable` control whether it starts at boot - two different things that get confused.
- A unit file describes the service: `ExecStart`, `Restart=on-failure`, `User`, `After=` and `Wants=` for ordering and dependencies, and environment either inline or from a file.
- Logs go to the journal, so `journalctl -u <unit>` and `-f` to follow, rather than hunting for a log file.

**Details and nuances**

```ini
[Unit]
Description=Provisioning agent
After=network-online.target

[Service]
ExecStart=/usr/local/bin/agent --config /etc/agent.conf
Restart=on-failure
RestartSec=5
User=agent

[Install]
WantedBy=multi-user.target
```

Two things worth knowing for an embedded or service role. systemd captures stdout and stderr into the journal by default, which is why a service should log to stdout and let the platform handle rotation and shipping - the same rule that makes container logs work. And `Restart=on-failure` plus a non-zero exit code is how a crash becomes an automatic restart, which is also how a crash loop becomes invisible unless someone watches the restart counter.

`systemctl status` shows the last few log lines with the state, which is usually enough to see why a start failed without opening the journal at all.

**Example or evidence boundary**

Production experience: systemd configuration on the NXP i.MX embedded Linux platform, alongside Yocto image work.

[↑ Back to question index](#question-index)

---

# 3. Shell

## Question NIX-010

[↑ Back to question index](#question-index)

### Question NIX-010 — How do redirection and pipes work?

**Short answer**

- Every process starts with three descriptors: 0 stdin, 1 stdout, 2 stderr. `>` redirects stdout to a file (truncating), `>>` appends, `<` feeds stdin from a file.
- `2>&1` points stderr at wherever stdout currently goes, and order matters: `cmd > f 2>&1` sends both to the file, while `cmd 2>&1 > f` sends stderr to the terminal and only stdout to the file.
- `|` connects one process's stdout to the next one's stdin, and the processes run concurrently rather than one after the other.

**Details and nuances**

```bash
cmd > out.txt 2> err.txt        # separate
cmd > all.txt 2>&1              # combined
cmd 2>/dev/null                 # discard errors only
cmd |& less                     # bash shorthand for 2>&1 |
```

Two details that come up. A pipeline's exit status is that of the *last* command, so `false | true` succeeds - `set -o pipefail` changes that, and `${PIPESTATUS[@]}` holds them all. And stdout to a file is block-buffered while stdout to a terminal is line-buffered, which is why a program's output appears instantly on screen and in delayed chunks when redirected; `stdbuf -oL` forces line buffering when you are tailing a redirected log.

[↑ Back to question index](#question-index)

---

## Question NIX-011

[↑ Back to question index](#question-index)

### Question NIX-011 — What is a shebang, and why `#!/usr/bin/env bash`?

**Short answer**

- The first line's `#!` tells the kernel which interpreter to run the file with; without it the file is executed by whatever shell invoked it, which may not be the one it was written for.
- `#!/bin/bash` names an absolute path that is right on most Linux systems and wrong where bash lives elsewhere; `#!/usr/bin/env bash` looks bash up on `PATH`, which is portable.
- `#!/bin/sh` means POSIX shell, not bash - so bash-only syntax such as arrays or `[[ ]]` will fail there, sometimes only on the machine where `/bin/sh` is dash.

**Details and nuances**

The trade-off with `env` is worth stating rather than treating it as simply better: it finds whichever interpreter comes first on `PATH`, which is the point when the right one is in a virtualenv or a user directory, and a hazard when `PATH` is not what you expected - including under `sudo`, which resets it.

The other half of making a script runnable is `chmod +x`, and the reason `./script.sh` needs the `./` is that the current directory is not on `PATH` - deliberately, so that a file named `ls` dropped in a directory cannot hijack the command.

[↑ Back to question index](#question-index)

---

## Question NIX-012

[↑ Back to question index](#question-index)

### Question NIX-012 — Which shell startup files run when?

**Short answer**

- A login shell reads `~/.bash_profile` (or `~/.bash_login`, or `~/.profile`); a non-login interactive shell reads `~/.bashrc`; a non-interactive shell running a script reads neither.
- That is why an alias or a `PATH` change works in your terminal and not in a script or over `ssh host command` - it was defined in a file that case does not read.
- The conventional fix is to put everything in `~/.bashrc` and have `~/.bash_profile` source it, so both paths end up in the same place.

**Details and nuances**

```bash
# ~/.bash_profile
[ -f ~/.bashrc ] && . ~/.bashrc
```

Zsh has the same structure with different names: `~/.zshenv` always, `~/.zprofile` for login, `~/.zshrc` for interactive, `~/.zlogin` after.

`export` is the other half: a variable without it exists only in the current shell, while an exported one is inherited by child processes - which is the whole reason `PATH`, `LD_LIBRARY_PATH` and friends are exported.

[↑ Back to question index](#question-index)

---

## Question NIX-013

[↑ Back to question index](#question-index)

### Question NIX-013 — What makes a shell script safe rather than merely working?

**Short answer**

- `set -euo pipefail` at the top: exit on an unhandled error, fail on an unset variable, and let a failure anywhere in a pipeline fail the pipeline.
- Quote every expansion - `"$var"`, `"$@"` - because an unquoted variable containing a space or a glob character is the single most common shell bug.
- Clean up with `trap ... EXIT` so a temporary directory is removed whether the script succeeded, failed or was interrupted.

**Details and nuances**

```bash
#!/usr/bin/env bash
set -euo pipefail

work="$(mktemp -d)"
trap 'rm -rf "$work"' EXIT

for f in "$@"; do
    [[ -f "$f" ]] || { echo "not a file: $f" >&2; exit 1; }
    process "$f" > "$work/$(basename "$f").out"
done
```

`set -e` has enough exceptions - it does not fire inside a condition, or for a command followed by `||` - that it is a safety net rather than a guarantee, and important checks should still be explicit. `rm -rf "$var"` where `$var` might be empty is the classic disaster, which `set -u` prevents by refusing to expand an unset variable at all.

For anything longer than a page, `shellcheck` finds most of this automatically and is worth naming as the tool you would put in CI.

[↑ Back to question index](#question-index)

---

## Question NIX-014

[↑ Back to question index](#question-index)

### Question NIX-014 — Where do you look when a service is misbehaving in production?

**Short answer**

- Logs first, in the order that narrows fastest: `journalctl -u <unit> --since "10 min ago"` on systemd, `/var/log/syslog` or `/var/log/messages` otherwise, and `dmesg` when the suspicion is the kernel, a driver or the OOM killer.
- Then resources: `df -h` for a full disk, `free -h` for memory, `top` for CPU, `ss -tulpn` for whether it is actually listening.
- Then the process itself: `systemctl status` for the restart count, `strace` or `gdb -p` for a hang, and the core dump if it crashed.

**Details and nuances**

A checklist in that order exists because the cheap causes are common: a full disk and the OOM killer between them explain a surprising share of "the service stopped working", and both are visible in one command. `dmesg | grep -i oom` settles the second immediately, and it is worth checking before theorising about the application, because a process that was killed by the kernel leaves no application log entry explaining why.

`journalctl -p err -b` - errors since boot - is the fastest broad look when you do not yet know which unit is at fault.

**Example or evidence boundary**

Production experience: log-driven diagnosis across application, SIP and system logs on embedded targets, and SSH plus `gdb` and core dumps on AWS EC2 Linux hosts for the C89 platform.

[↑ Back to question index](#question-index)
