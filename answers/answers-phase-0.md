# Answer Key — Phase 0 (Foundations, Days 0a–0e)

Trainer prep companion. Answers are grounded in each day's theory section; ⚠ marks
answers drawn from general knowledge beyond the day file — verify before teaching.

---

## Day 0a — What a Computer and an Operating System Actually Are

**Warm-up:** Day 0a has no prior-material warm-up; it opens with 3 baseline questions
instead (no fixed answers — trainer judges the response, doesn't correct hard):
1. Open-ended: gauge their current mental model of boot (many will say "it just turns
   on" — that's fine, it's why Phase 0 exists).
2. Open-ended: gauge whether they already have any hardware/software split at all.
3. Open-ended: gauge prior Linux exposure (servers/Android/none) — shapes how much you
   can lean on analogies later.

**Recall:**
1. The four jobs: **process management** (running many programs by switching between
   them), **memory management** (private RAM per program), **file management**
   (organizing data into files/folders so location is abstracted away), **device
   management** (one common interface to keyboard/screen/disk/network).
2. The **kernel** (Linux) is the core engine — process/memory/file/device management at
   the lowest level. A **distro** (Fedora, RHEL, Ubuntu) is that kernel bundled with
   userland tools, a package manager, and defaults — the thing a human actually installs.
3. Any one of: nearly all servers/cloud infrastructure run Linux, so it's the floor for
   ops/DevOps work; scripting and automation (the core of DevOps) is native to Linux's
   CLI; certifications (RHCSA/RHCE) and job postings assume Linux fluency specifically,
   not "general computer literacy."
4. Open-ended — accept any device with an embedded OS the student can reason about:
   smart TVs, home routers, some car infotainment systems, Android phones/tablets, smart
   fridges/appliances. ⚠ Not all of these are confirmed Linux-based per device/vendor —
   accept reasonable guesses, the point is the reasoning ("has a screen + does 'smart'
   things" → likely a full OS underneath), not a verified list.
5. CLI is faster once learned (no reaching for a mouse for repetitive work), it's
   **scriptable** (a saved sequence of commands can be replayed — the seed of
   automation), and most real servers have no screen/mouse attached at all — CLI over a
   network connection (SSH, covered later) is the only way in.

---

## Day 0b — The Terminal and the Shell (First Hands-On)

**Recall:**
1. The **terminal** is the window; the **shell** (bash) is the program inside it that
   reads and interprets what you type.
2. `command`, `-options` (short `-l` or long `--all`, change *how* it behaves),
   `arguments` (*what* it acts on).
3. `man <command>` (full manual) and `<command> --help` (quick summary).
4. `ls -a` additionally shows hidden entries — files/directories whose name starts
   with a dot, which plain `ls` skips by convention.
5. The shell looks for a program matching that name on disk, doesn't find one, and
   prints "command not found" — nothing is broken, it's an honest failed lookup.

## Day 0c — Files and the Filesystem Tree

**Recall:**
1. The forward slash, `/`.
2. Any three of: `/home` (user files), `/etc` (config), `/var` (changing data/logs),
   `/tmp` (scratch, may be wiped), `/usr` (installed software), `/opt` (third-party
   software), `/bin` (essential command binaries).
3. `mkdir` creates a **directory**; `touch` creates an **empty file** (or updates an
   existing file's modified timestamp without changing its contents).
4. There's no OS-level recycle bin/undo for `rm` — it deletes immediately and
   permanently by design; the caution has to come from the person running it, not a
   safety net the system provides.
5. Prints a file's entire contents to the screen.

## Day 0d — Paths: Absolute vs Relative

**Recall:**
1. The forward slash, `/` — a path starting with it is absolute.
2. Because a relative path is resolved against your **current working directory**;
   the same text (e.g. `home`) points at a different destination depending on where
   you were standing when you typed it — there's no fixed meaning independent of
   location.
3. Your own home directory, absolute, regardless of current location.
4. `cd ..` moves up one level (to the parent directory); `cd .` stays exactly where
   you are (`.` means "here").
5. Returns you to the previous directory you were in before your last `cd` — a
   quick toggle between two locations.

## Day 0e — Users, Root, and System States (Bridge to Day 1)

**Recall:**
1. It runs one specific command with root's (superuser's) full privileges, without
   requiring you to log in as root.
2. Because root has no restrictions — a single mistake (typo, wrong path) in a root
   shell can damage or delete something critical with no safety net in the way.
3. **Root the user/account** (the unrestricted superuser) vs **root filesystem**
   (the actual disk/partition mounted at `/`, the top of the directory tree) — same
   word, different referents.
4. `multi-user.target` is a full system with networking/services but no graphical
   desktop; `graphical.target` is multi-user.target plus a desktop environment
   (windows, mouse).
5. ⚠ Servers run headless (no monitor/keyboard attached, managed remotely over
   SSH) and a graphical desktop consumes resources (CPU/RAM/GPU) with no one present
   to use it — multi-user.target is the efficient, appropriate default for a machine
   nobody sits in front of.

---
