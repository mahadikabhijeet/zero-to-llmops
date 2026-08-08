# Day 0c — Files and the Filesystem Tree

Phase 0 · Foundations, day 3 of 5. Format: ~30 min theory + ~45 min lab. Distro:
RHEL/Fedora/CentOS.

---

## Recall warm-up (5 min)
1. What's the difference between a terminal and a shell?
2. What are the three parts of a command's shape?
3. Which two ways can you ask a command for help?

## Theory (~30 min)

### A. "Everything is a file" (8 min)
- In Linux, almost everything is represented as a file: your documents, yes, but
  also devices (a disk shows up as a file), and even live information from the
  kernel (we'll see this literally on Day 0e/Day 1). This is a deliberate design
  choice — one consistent way to interact with very different things.
- A **directory** (what other systems call a "folder") is just a special kind of
  file whose job is to hold references to other files and directories.

### B. One tree, starting at `/` (12 min)
- Every single file and directory on a Linux system hangs off **one root**, written
  as a single forward slash: `/`. Not "C:\" — one tree, one root, always.
- Walk the important branches at a *concept* level (we'll use them for real over the
  next few days):
  - `/home` — regular users' personal files.
  - `/etc` — system configuration files (settings live here).
  - `/var` — data that changes/grows over time (logs, for example).
  - `/tmp` — temporary scratch space; can be wiped on reboot.
  - `/usr` — most installed software and its supporting files.
  - `/opt` — optional/third-party software, often installed by hand.
  - `/bin` — essential command programs (many of the commands you ran yesterday
    physically live here).
- You don't need to memorize all of this today — just recognize that the tree has
  *structure*, and each branch has a *purpose*. It'll click with repetition.

### C. Making and looking at files (10 min)
- `mkdir <name>` — make a directory.
- `touch <name>` — create an empty file (or update its timestamp if it exists).
- `ls -l` — list with detail: permissions, size, last-modified time.
- `cat <file>` — print a file's entire contents to the screen.
- `rm <file>` — remove a file. **Said with respect**: there is no recycle bin, no
  undo. When we introduce `rm -r` (remove a whole directory) later, treat it like a
  loaded tool — always double-check what you're pointing it at before pressing
  Enter.

## Lab (~45 min)

### Part 1 — Walk the tree, just looking (15 min)
1. Run `ls /` — this is the root, the top of everything. Read the names out loud;
   which ones do you recognize from the theory section?
2. `ls /home` — see your own username as a directory there (or your user's folder).
3. `ls /etc | head -20` — a glimpse of configuration files (don't open/edit
   anything yet, just look).
4. `pwd` — confirm where your terminal currently sits (probably somewhere under
   `/home`, your own space — the safest place to practice).

### Part 2 — Make your own small tree (20 min)
5. From your home directory, run `mkdir practice` then `cd practice` (don't worry
   about `cd` mechanics yet — full lesson tomorrow; for now it just means "go into
   that directory").
6. `mkdir notes` and `mkdir notes/day1` — you just made a directory inside a
   directory.
7. `touch notes/day1/hello.txt` — an empty file now exists.
8. `ls -l notes/day1` — confirm `hello.txt` is there, size 0.
9. `echo "my first file" > notes/day1/hello.txt` — this overwrites the empty file
   with a line of text (full explanation of `>` comes later; for now just see that
   it worked).
10. `cat notes/day1/hello.txt` — read it back, confirm your text is there.

### Part 3 — Remove, carefully (10 min)
11. `touch notes/day1/scratch.txt` then immediately `rm notes/day1/scratch.txt` —
    practice removing a file you just made yourself, low stakes.
12. `ls -l notes/day1` — confirm `scratch.txt` is gone and `hello.txt` remains.
13. Say out loud, before running anything: "if I ran `rm` on the wrong file right
    now, could I get it back?" (Answer: no — that's the point of this checkpoint.)

## Definition of done
Can explain "everything is a file" and name the purpose of `/home`, `/etc`, `/var`,
`/tmp`, `/usr`, `/opt` in their own words · has made a directory, made a file inside
it, written text into it, read it back, and deleted a file on purpose · treats `rm`
with visible caution.

## Recall questions (write into NotebookLM for the 1–3–7–21 cycle)
1. What character represents the root of the filesystem tree?
2. Name three of the big top-level directories and what each is for.
3. What's the difference between `mkdir` and `touch`?
4. Why is there no "undo" for `rm`, unlike a desktop recycle bin?
5. What does `cat` do?

## Homework
Build a small folder tree of your own choosing (at least 2 levels deep, at least 3
files total) somewhere under your home directory. Be ready to `ls -l` through it and
explain what's where.

## Day 0d preview
Tomorrow: paths. You've been typing short names like `notes/day1` — tomorrow we
learn the full picture of absolute vs relative paths, so you're never lost in the
tree again.

Related: [[README|devops-training]] · [[foundations-phase]] · [[day-0b]]
