# Day 0a — What a Computer and an Operating System Actually Are

Phase 0 · Foundations, day 1 of 5 — before Day 1. Format: ~45 min theory + ~30 min
guided discussion (no terminal yet — pure mental model). No distro needed today.

---

## Recall warm-up (5 min)
No prior foundations material — three baseline questions instead (open-ended, no
fixed answers, just gauge where the student is starting from):
1. When you turn on a phone or laptop, what do you think actually happens in the
   first few seconds before you see the home screen / desktop?
2. What's the difference between "hardware" and "software," in your own words?
3. Have you heard of Linux before? Where — servers, Android, somewhere else?

## Theory (~45 min)

### A. Hardware vs software (10 min)
- **Hardware** = the physical parts you could drop on your foot: CPU (does the
  thinking, executes instructions), RAM (short-term memory, empty when powered off),
  storage/disk (long-term memory, keeps data without power), keyboard/screen/network
  card (input/output).
- **Software** = instructions stored on disk that tell the hardware what to do.
  Two kinds: **system software** (runs the machine itself — the operating system) and
  **application software** (things you use — a browser, a game, a spreadsheet).
- Analogy: hardware is the car's engine, wheels, and dashboard. Software is the
  driver's instructions. No driver, the car does nothing, no matter how good the
  engine is.

### B. What an operating system does (15 min)
- The OS is the **traffic cop** between programs and hardware. Programs never touch
  hardware directly — they ask the OS, and the OS decides who gets the CPU next, who
  gets memory, who gets to read a file, in what order.
- Four jobs, in plain language:
  1. **Process management** — running many programs "at once" by rapidly switching
     between them (even on one CPU core).
  2. **Memory management** — giving each program its own private slice of RAM so one
     program's mistake doesn't corrupt another's.
  3. **File management** — organizing data on disk into files and folders so programs
     don't need to know *where* on the physical disk something sits.
  4. **Device management** — talking to the keyboard, screen, disk, network card
     through a common interface, so a program doesn't need a different plan for every
     brand of keyboard.
- Examples of operating systems the student already knows: Windows, macOS, Android
  (Android's core *is* Linux), iOS. All OSes do the same four jobs differently.

### C. What Linux is, and why this course uses it (12 min)
- **Linux is an operating system kernel** created by Linus Torvalds in 1991 — free,
  open-source (anyone can read/modify/redistribute the source code, unlike Windows).
- A **distro** (distribution) = the Linux kernel + a bundle of tools + a package
  manager + defaults, packaged for humans to actually install and use. Examples:
  Fedora, RHEL (Red Hat Enterprise Linux — what this course uses), Ubuntu, Debian.
- Why it matters for a career in ops/DevOps: the overwhelming majority of servers,
  cloud infrastructure (AWS/Azure/GCP), and Android phones run on Linux. If you want
  to work with servers or the cloud, Linux is not optional — it's the floor everyone
  stands on.
- We use **RHEL/Fedora** specifically because it's the enterprise standard, it has a
  respected certification path (RHCSA/RHCE, later in this course), and its package
  family (`dnf`/`rpm`) is common in real companies.

### D. GUI vs CLI — the fork in the road (8 min)
- **GUI** (Graphical User Interface) — windows, icons, mouse clicks. Friendly, but
  slow for repetitive or precise tasks, and hard to automate.
- **CLI** (Command Line Interface) — typed text commands. Looks intimidating at
  first, but it's faster once learned, it's **scriptable** (you can save a sequence
  of commands and re-run it — this is the seed of automation, which is the entire
  point of DevOps), and it's how servers are actually managed (most servers don't
  even have a screen or mouse attached).
- This course lives almost entirely in the CLI starting tomorrow (Day 0b). Today was
  the *why*; tomorrow is the *how*.

## Guided discussion (~30 min, no terminal)
Talk through these out loud with the trainer — no typing, just reasoning:
1. Walk through, in order, everything that (probably) happens between pressing the
   power button and seeing a login screen. Don't worry about being exactly right —
   this is a first guess we'll correct properly starting Day 0e and finishing on the
   existing Day 1.
2. Pick 3 apps on your phone. For each, guess: is it doing something the OS itself
   also needs to do (process/memory/file/device management), or is it just using
   those services?
3. Name 5 devices around you (home, work, car, appliances) you think might be running
   Linux under the hood. (Hint: anything with a screen and "smart" in the name is a
   good guess — TVs, routers, some cars' entertainment systems.)

## Definition of done
Can explain, in their own words and without notes: the hardware/software split; the
OS's four jobs; what makes Linux a kernel and a distro different things; why this
course chooses CLI over GUI. No terminal use required yet — that starts Day 0b.

## Recall questions (write into NotebookLM for the 1–3–7–21 cycle)
1. Name the four jobs of an operating system.
2. What's the difference between the Linux kernel and a Linux distro (e.g. Fedora)?
3. Give one reason a DevOps engineer needs to know Linux specifically (not just
   "computers" in general).
4. Name 5 devices you think run Linux, and why you think so.
5. Why is CLI generally preferred over GUI for managing servers?

## Day 0b preview
Tomorrow we stop talking *about* the terminal and open one for real — the shell,
your first real commands (`whoami`, `pwd`, `ls`), and how to ask for help
(`man`, `--help`) so you're never stuck memorizing.

Related: [[README|devops-training]] · [[foundations-phase]] · [[day-001]]
