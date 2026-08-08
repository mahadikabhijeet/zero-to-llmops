# Day 0e — Users, Root, and System States (Bridge to Day 1)

Phase 0 · Foundations, day 5 of 5 — last day before revisiting Day 1. Format:
~30 min theory + ~35 min lab + 10-min Day 1 callback. Distro: RHEL/Fedora/CentOS.

---

## Recall warm-up (5 min)
1. What's the one character that tells you a path is absolute?
2. What does `~` always mean?
3. What's the difference between `cd ..` and `cd .`?

## Theory (~30 min)

### A. What a user is (8 min)
- Linux is **multi-user** by design — many people (or many automated services) can
  have their own account on the same machine, each with their own home directory
  (you've been living in yours all week: `~`, i.e. `/home/<you>`), their own files,
  their own permissions.
- Every user has a **username** (what you see from `whoami`) and, under the hood, a
  numeric **user ID (UID)**. You'll meet UIDs properly later; for now just know the
  username is a friendly label for a number the system actually tracks.

### B. Root, the superuser, and `sudo` (12 min)
- **root** is the one special account that can do *anything* — read/write/delete any
  file, change any setting, no restrictions. Every other user is deliberately
  limited so a mistake (or a compromised account) can't destroy the whole system.
- Working directly *as* root all the time is dangerous — one typo in a root shell
  can wipe critical files with no safety net. So the standard practice is: **log in
  as a normal user, and use `sudo` to borrow root's power for a single command,**
  one at a time, with a password prompt each time as a speed bump.
- `sudo <command>` — "do this one command as root." Example: `sudo dnf install
  <package>` (installing software normally requires root). The password prompt
  isn't the system being difficult — it's a deliberate pause before something
  powerful happens.
- Rule for this course: if a command needs `sudo`, read it twice before pressing
  Enter. That pause is the entire safety mechanism.

### C. The "root filesystem" (5 min)
- You now have the vocabulary to understand a term from Day 1 that felt floating
  before: the **root filesystem** is simply the actual disk/partition that gets
  mounted at `/` — the physical home of the tree you've been navigating all week
  with `cd`, `ls`, and paths. "Root" here means the same `/` you already know, not
  the root *user* — same word, two related meanings, context tells you which.

### D. System targets — how much of the machine is turned on (5 min)
- A Linux machine can boot into different **targets** — think of it as "how much of
  the system wakes up." Two you'll meet immediately on Day 1:
  - **multi-user.target** — full multi-user system, network, services — but **no
    graphical desktop**. This is what almost every real server runs; nobody pays
    for a GPU and a desktop environment on a machine nobody sits in front of.
  - **graphical.target** — everything multi-user.target has, **plus** a desktop
    environment with windows and a mouse (what your own laptop probably boots into).
- Why this matters for your career: production servers are overwhelmingly
  multi-user.target — text-only. This is exactly why Day 0b's CLI comfort wasn't
  optional practice — it's the only interface most servers you'll ever touch will
  give you.

## Lab (~35 min)

### Part 1 — Who and what you are (10 min)
1. `whoami` — confirm your username (recall from Day 0b).
2. `id` — see your full identity: your UID, your group, and the groups you belong
   to. Read the output out loud and identify the UID number.
3. `sudo whoami` — enter your password when prompted; the output should print
   `root` — you just ran one command *as* root, without ever logging in as root.

### Part 2 — Feel the difference sudo makes (15 min)
4. Try `cat /etc/shadow` (a file that stores password hashes) as your normal user —
   note the "permission denied" style error.
5. Now try `sudo cat /etc/shadow` — it works. Discuss out loud: why should this file
   specifically be locked down so tightly?
6. Try creating a file in a protected location: `touch /etc/testfile` (should fail),
   then `sudo touch /etc/testfile` (should succeed), then clean up immediately:
   `sudo rm /etc/testfile`. Narrate each step before running it.

### Part 3 — See your current target (10 min)
7. Run `systemctl get-default` — this shows which target your machine boots into by
   default. Say out loud whether you expected multi-user or graphical, and why,
   given what kind of machine you're on right now.

## Definition of done
Understands root is an account, not a place · uses `sudo` deliberately and reads
the command before confirming · can explain "root filesystem" without confusing it
with the root user · knows the difference between multi-user.target and
graphical.target and why servers default to the former.

## Recall questions (write into NotebookLM for the 1–3–7–21 cycle)
1. What does `sudo` actually do, in one sentence?
2. Why is working directly as root all the time considered risky?
3. What are the two meanings of "root" you now need to keep separate?
4. What's the difference between `multi-user.target` and `graphical.target`?
5. Why do most real servers run without a graphical target at all?

## Homework
Run `whoami` and `id`, and write 3–4 sentences in your own words explaining why
`sudo` matters — as if explaining it to a friend who's never touched Linux.

## Day 1 callback (10 min, do NOT re-teach Day 1 — just bridge)
Day 1 (already covered) walked through firmware → GRUB → kernel → initramfs →
systemd (PID 1) → targets → journald. Say this bridge, then move on:
"Now that you've got files, paths, users, root, and targets in your hands, that
boot story isn't abstract anymore. Firmware hands off to GRUB, GRUB loads the
kernel plus a temporary root filesystem called initramfs, the kernel mounts the
*real* root filesystem — the same `/` you've been living in all week — and then
starts PID 1, systemd, which decides whether to boot into multi-user.target or
graphical.target, exactly like you just checked with `systemctl get-default`. Same
words, now solid ground under them." Ask them to explain the boot sequence back to
you, unprompted — if it lands cleanly, Phase 0 has done its job and Day 2 continues
as originally planned.

Related: [[README|devops-training]] · [[foundations-phase]] · [[day-0d]] · [[day-001]]
