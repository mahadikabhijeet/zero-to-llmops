# Day 0d — Paths: Absolute vs Relative

Phase 0 · Foundations, day 4 of 5. Format: ~25 min theory + ~50 min lab. Distro:
RHEL/Fedora/CentOS. This is the single idea that unblocks everything later — take
the extra lab time.

---

## Recall warm-up (5 min)
1. What character represents the root of the filesystem tree?
2. Name three top-level directories and what each is for.
3. What's the difference between `mkdir` and `touch`?

## Theory (~25 min)

### A. Absolute paths — the full address (8 min)
- An **absolute path** always starts with `/`, the root. It's the complete address
  from the very top of the tree, and it means the same thing **no matter where you
  currently are**. `/home/student/notes/day1/hello.txt` always points at the exact
  same file, whether you're sitting in `/`, in `/tmp`, or anywhere else.
- Rule of thumb: if a path starts with `/`, it's absolute — full address, always
  reliable, slightly longer to type.

### B. Relative paths — from where you're standing (8 min)
- A **relative path** does *not* start with `/`. It's directions from **wherever
  you currently are** (your **current working directory**, what `pwd` shows you).
  `notes/day1/hello.txt` only makes sense relative to your current location — the
  same text means something different depending on where you're standing when you
  type it.
- This is exactly what you did on Day 0c without naming it — `notes/day1/hello.txt`
  was a relative path the whole time.

### C. The four special symbols (9 min)
- `.` — "here," the current directory itself.
- `..` — "up one level," the parent directory.
- `~` — shorthand for **your home directory**, absolute regardless of where you are
  (`~/notes` always means your own notes folder).
- `-` (used with `cd -` specifically) — "the previous directory I was just in," a
  quick way to bounce back and forth between two places.
- These aren't extra syntax to memorize separately — they're just relative paths
  with a couple of reserved shortcuts.

## Lab (~50 min)

### Part 1 — Prove the absolute/relative difference to yourself (15 min)
1. `cd ~` (go home), then `pwd` — note the absolute path shown.
2. `cd /` (go to root), then run `ls home` — a relative path from `/`, listing
   what's inside `/home`.
3. From `/`, now run `ls /home` — the same result, but an absolute path. Confirm:
   same destination, two different ways of describing it, because your current
   location happened to make the relative one work too.
4. `cd /tmp`, then try `ls home` again — this time it likely fails or shows nothing
   useful, because relative to `/tmp`, there's no `home` folder inside it. Absolute
   `ls /home` still works from anywhere. This is the whole lesson in one experiment.

### Part 2 — Move around deliberately with the special symbols (20 min)
5. `cd ~/practice/notes/day1` (from Day 0c's tree — recreate it if needed) — go
   there directly using an absolute-from-home path.
6. `pwd` to confirm. Then `cd ..` — you should now be in `.../notes`. `pwd` again.
7. `cd ..` once more — now in `.../practice`. `pwd`.
8. `cd .` — nothing should change; prove it with `pwd` before and after.
9. `cd ~` to jump home directly. Then `cd -` — you should land back in
   `.../practice`, the last place you were. `cd -` again — back home. Do this
   toggle 2–3 times until it feels automatic.

### Part 3 — Get lost on purpose, then get found (15 min)
10. Run `cd /var/log` (or any unfamiliar directory), then `cd ../../etc`, then
    `cd ./../` — chain a few relative moves without checking `pwd` in between.
11. Now run `pwd` — were you right about where you'd land? Reason out loud what
    each `..` and `.` did before confirming.
12. Whenever genuinely unsure: `pwd` tells you where you are, `cd ~` always gets
    you home, safe, no matter how lost you get. That combination is your safety
    net for the rest of this course.

## Definition of done
Can state, unprompted, whether a given path is absolute or relative and why · uses
`.`, `..`, `~`, `-` correctly without hesitating · can deliberately navigate away
and find the way back using only `pwd` and `cd ~`.

## Recall questions (write into NotebookLM for the 1–3–7–21 cycle)
1. What's the one character that tells you a path is absolute?
2. Why does the same relative path (e.g. `home`) behave differently depending on
   where you run it from?
3. What does `~` always mean, regardless of current location?
4. What's the difference between `cd ..` and `cd .`?
5. What does `cd -` do?

## Homework
Pick 5 real locations on your machine (e.g. `/etc`, `/var/log`, your home directory,
a subfolder of `practice`, `/tmp`). For each, navigate to it two ways: once with a
full absolute path, once with a relative path from wherever you currently are.
Write down both commands for each of the 5.

## Day 0e preview
Tomorrow: users, root, and the idea of a "root filesystem" and system targets — the
last bridge before we revisit Day 1's boot story with all the pieces finally in
place.

Related: [[README|devops-training]] · [[foundations-phase]] · [[day-0c]]
