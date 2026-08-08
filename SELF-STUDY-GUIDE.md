# Self-Paced Study Guide

How to work through this curriculum on your own, without waiting for a video.

The written lessons are complete. Everything taught in a recorded session is in the
day file: the theory, the exact commands, the lab, and the check for whether you got
it. The videos add pacing, tone, and the occasional live mistake worth seeing, but
nothing in them is required to finish this course.

---

## 1. Set your pace honestly

"180 days" is a sequence, not a deadline. Almost nobody does one day per day.

| Your situation | Realistic pace | Finish in |
|---|---|---|
| Full-time study | 1 day per day, 5 days a week | 8–9 months |
| Full-time job, serious about it | 3 days per week | 14–16 months |
| Full-time job, evenings only | 2 days per week | ~2 years |

Pick the row that matches your actual life, not the one you wish were true. The
failure mode is not going slowly. It is skipping labs to keep up with a number, which
produces someone who has read about Kubernetes and cannot use it.

**One rule that matters more than pace:** never do two new curriculum days in one
sitting. Depth beats coverage. Half a day done properly beats two days rushed.

## 2. The loop for a normal day

Budget about 3 hours for a full day file. If you have 90 minutes, do half and continue
next session rather than skimming the whole thing.

1. **Recall warm-up, 5 minutes, from memory.** Answer the questions at the top out
   loud before looking anything up. Getting them wrong is useful information. Do not
   skip this because it feels trivial.
2. **Theory, about 1 hour.** Read it once through without touching the keyboard. Then
   read it again with a terminal open, typing each command as you meet it.
3. **Lab, about 2 hours.** Type the commands. Do not copy and paste.
4. **Project step.** Where a day says the incremental project changes, make that change
   and commit it.
5. **Definition of done.** Read it and answer honestly. If you cannot do what it lists,
   you are not done, and moving on will cost you later.
6. **Recall questions.** Put them into your spaced repetition system before you close
   the file. See [`NOTEBOOKLM-GUIDE.md`](NOTEBOOKLM-GUIDE.md).

### Type the commands, do not paste them

This is the single most common way people waste months on a course like this. Copying
a command teaches you that the command exists. Typing it teaches you the command.
Typing it wrong, reading the error, and fixing it is the thing that actually makes you
employable, because that is the entire job.

## 3. How to use the answer keys

`answers/answers-phase-N.md` covers the recall questions and lab checkpoints for that
phase.

The order is: attempt, then struggle, then check. Not attempt, check, then struggle.

Concretely, when you are stuck on a lab step:

1. Re-read the error message properly. Most of them say exactly what is wrong.
2. Try `man <command>` or `<command> --help`. Learning to read a man page is itself a
   Phase 0 skill and it pays for the rest of your career.
3. Give it a genuine 15 minutes.
4. Then open the answer key.

If you look at the answer inside the first minute, you get the feeling of learning
without the substance. You will notice this later as the sense that you "did" a phase
but cannot do any of it from memory.

## 4. Review days are not optional

Every day divisible by 7 is a review day. No new material.

The temptation is to skip them because nothing new happens. Do not. They exist because
of how forgetting works: material you met once and never revisited is largely gone
within a month. The review day is where the previous six days stop being things you
read and start being things you know.

On a review day:

- Take the cumulative quiz from memory, closed book
- Redo the single lab you were weakest on, from a clean state
- Tidy and commit the incremental project
- Catch up on any spaced repetition you have fallen behind on

## 5. The incremental project

From Day 1 you build one thing that grows for the entire course, living at
`/opt/fresher_project`. It begins as a shell script that logs boot information. By the
end it is a real system with services, containers, a pipeline, monitoring, and a model
being served.

Treat it as the spine. Two specific pieces of advice:

- **Put it in Git from Day 1**, even before the course formally introduces Git in
  Phase 2. Commit at the end of every session with a message saying what changed. By
  Phase 6 this history is the most convincing thing you own.
- **Never rebuild it from scratch to "do it properly."** The messy version that grew
  with you teaches more than a clean rewrite, and the growth is the point.

The project is also what you will talk about in interviews. A candidate who can say
"I built this over 180 days, here is the commit where I broke it and here is how I
fixed it" is in a completely different category from one who lists tools on a resume.

## 6. When you get properly stuck

In order:

1. **Read the error out loud.** Sounds silly, works constantly.
2. **Check the obvious three:** are you the right user, are you in the right
   directory, is the service actually running.
3. **Isolate.** Run the smallest possible version of what failed.
4. **Search the exact error string**, in quotes, minus your specific paths.
5. **Ask an AI tutor to explain, not to fix.** Paste the error and ask "what is this
   telling me and what should I check next?" rather than "give me the command." The
   first builds the skill, the second borrows it.
6. **Open an issue on this repo.** Include your distro and version, the exact command,
   and the full error text.

A note on AI: you will be using it constantly in your career and there is no virtue in
avoiding it. But during the first three phases, use it to explain and to review, not to
produce commands you then paste blind. The muscle you are building is diagnosis, and
that is the one thing that does not transfer if you outsource it early.

## 7. Distro differences

Sessions are recorded on **CentOS Stream 9**. Rocky Linux, AlmaLinux and RHEL behave
identically. If you are on Ubuntu or Debian, the translations you will need most:

| CentOS / RHEL | Ubuntu / Debian |
|---|---|
| `dnf install X` | `apt install X` |
| `dnf remove X` | `apt remove X` |
| `rpm -q X` | `dpkg -l X` |
| `firewalld` / `firewall-cmd` | `ufw` |
| `/etc/sysconfig/` | `/etc/default/` |
| `httpd` | `apache2` |
| SELinux enabled by default | AppArmor instead |

Everything about systemd, permissions, text processing, networking, and every phase
from 2 onward is the same on both. The SELinux days are the main place Ubuntu users
should read along rather than follow along.

## 8. Tracking yourself

Keep a plain text file. One line per session:

```
2026-08-08  day-004  permissions/SUID/ACL  done  weak on: umask arithmetic
2026-08-11  day-005  grep + regex          done  weak on: BRE vs ERE escaping
```

The "weak on" column is the valuable part. On review days, go straight to those. After
a phase, the list tells you honestly what to redo before moving up a level.

## 9. What finishing actually looks like

You are not done with a phase when you have read the last day file. You are done when
you can:

- Rebuild that phase's project state on a fresh machine without the notes
- Explain each major concept out loud to somebody who does not know it
- Answer the phase's recall questions cold, weeks later

If you can do those three, move on with confidence. If you cannot, the fix is a week
of review, and that week will save you a month later.
