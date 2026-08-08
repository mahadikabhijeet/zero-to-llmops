# Day 0b — The Terminal and the Shell (First Hands-On)

Phase 0 · Foundations, day 2 of 5. Format: ~30 min theory + ~45 min lab. First time
opening a real terminal. Distro: RHEL/Fedora/CentOS (or any Linux terminal available).

---

## Recall warm-up (5 min)
1. What are the four jobs of an operating system? (process, memory, file, device)
2. What's the difference between the Linux kernel and a distro?
3. Why did we say CLI over GUI for servers?

## Theory (~30 min)

### A. Terminal, shell, command — getting the words right (10 min)
- **Terminal** = the window you type into. It used to be a physical device; now it's
  a program that gives you that window.
- **Shell** = the program running inside the terminal that actually reads what you
  type and does something with it. Ours is called **bash** (Bourne Again SHell).
  Terminal is the stage, shell is the actor.
- **Command** = one instruction to the shell. Every command has the same shape:
  `command -options arguments`. Example: `ls -l /home` — `ls` is the command, `-l`
  is an option (changes *how* it behaves), `/home` is the argument (*what* it acts
  on). Options come short (`-l`, one dash one letter) or long (`--all`, two dashes
  one word) — same idea, two spellings.
- There is no hidden magic — a command is just a program's name. When you type `ls`,
  the shell finds a program called `ls` on disk and runs it. That's all a command is.

### B. The number one skill: asking for help (10 min)
- You will never memorize every command or every option, and you don't need to.
  What you need is knowing **how to ask**.
- `man <command>` opens the manual page — the full reference. `man ls`.
- `<command> --help` gives a shorter, quick-reference summary. `ls --help`.
- If a command feels stuck or you want to stop it, `Ctrl+C` cancels it.
- Rule for this whole course: **when in doubt, `man` it before asking the trainer.**
  Reading a man page and figuring it out yourself is a core ops skill on its own.

### C. Six safe first commands (10 min)
- `whoami` — who am I logged in as?
- `date` — what's the system's current date/time?
- `pwd` — print working directory: where am I right now in the filesystem?
- `ls` — list what's in the current location.
- `echo <text>` — print text back to the screen (useful for testing, scripting later).
- `clear` — wipe the screen so you're not staring at old output (nothing is deleted,
  just visually cleared).
- None of these six can break anything. That's deliberate — the goal today is
  comfort typing, not caution yet. Caution starts tomorrow when we touch real files.

## Lab (~45 min)

### Part 1 — Open a terminal and get oriented (10 min)
1. Open a terminal application. Take a moment to notice the **prompt** — the text
   right before your cursor (often `username@hostname:~$` or similar). It's telling
   you who you are and roughly where you are, before you even type anything.
2. Run `whoami`. Then `date`. Then `pwd`. Read each output out loud and say in your
   own words what it told you.

### Part 2 — Look around without changing anything (15 min)
3. Run `ls`. Then `ls -l` (long format — more detail per item). Then `ls -a`
   (shows hidden items too, the ones starting with `.`).
4. Run `echo hello`. Then `echo $USER` — notice `$USER` gets replaced by your
   username; that's your first taste of a **variable**, much more on this later.
5. Run `clear`, confirm the screen wipes, then run `pwd` again to prove nothing
   about your location changed — clearing the screen is purely visual.

### Part 3 — Getting help, for real (15 min)
6. Run `man ls`. Scroll with the arrow keys or spacebar, find the `-a` and `-l`
   flags in the manual, confirm they match what you saw in Part 2. Press `q` to quit
   the man page.
7. Run `ls --help | head -20` — a shorter summary of the same information.
8. Run `man -k directory` — this **searches** man pages by keyword when you don't
   know a command's exact name. Skim the results.

### Part 4 — Typing practice, no fear (5 min)
9. Freely try `whoami`, `date`, `pwd`, `ls -l`, `echo <anything>` a few more times in
   any order. Deliberately mistype a command once (e.g. `lls`) and read the "command
   not found" error — it's not a machine breaking, it's the shell not found a match.

## Definition of done
Comfortable typing a command and pressing Enter without hesitation · can explain
terminal vs shell vs command in their own words · has run all six safe commands
independently · knows to reach for `man`/`--help` before panicking or asking.

## Recall questions (write into NotebookLM for the 1–3–7–21 cycle)
1. What's the difference between a terminal and a shell?
2. What are the three parts of a command's shape (`command -options arguments`)?
3. Which two ways can you ask for help on a command you don't know?
4. What does `ls -a` show that plain `ls` doesn't?
5. What happens, technically, if you mistype a command name?

## Homework
Run 10 different commands (any mix of today's six, repeated with different
options/arguments counts as different). Take a screenshot of your terminal showing
all 10 and their output.

## Day 0c preview
Tomorrow: "everything is a file." We stop just looking, and start making files and
folders of our own — and meet the big branches of the filesystem tree.

Related: [[README|devops-training]] · [[foundations-phase]] · [[day-0a]]
