# Zero to LLMOps

A free, 180-day curriculum that takes you from "I have never used a terminal" to
running ML and LLM systems in production. Every lesson, every lab, and every answer
key is in this repo. The video series is the companion, not the requirement.

**YouTube channel:** [Zero to LLMOps](https://www.youtube.com/@zerotollmops)
**Playlist:** [DevOps Zero → LLMOps · Foundations](https://www.youtube.com/playlist?list=PLZczW9nvHiLc)

---

## Why this exists

Most DevOps courses stop at Kubernetes. Most MLOps courses assume you already know
Linux, networking, CI/CD and cloud. There is very little that walks a complete
beginner all the way to LLMOps in one continuous line, and almost nothing that shows
the failures honestly.

This curriculum is that line. It was written to teach one real person from scratch,
recorded as it happened, and the written material is here so you are never dependent
on a video being watched at the right speed, in the right order, or at all.

If you can only take one thing from this repo: the lessons are self-contained. Read
`lessons/`, do the labs, check yourself against `answers/`. The videos help, but the
text is the source of truth.

## Start here

| If you are | Start at |
|---|---|
| New to computers or the terminal | [`lessons/phase-0-foundations/day-0a.md`](lessons/phase-0-foundations/day-0a.md) |
| Comfortable with a terminal already | [`lessons/phase-1-linux-networking-bash/day-001.md`](lessons/phase-1-linux-networking-bash/day-001.md) |
| Wanting to plan your route first | [`curriculum.md`](curriculum.md) |
| Studying without the videos | [`SELF-STUDY-GUIDE.md`](SELF-STUDY-GUIDE.md) |
| Using NotebookLM or another AI tutor | [`NOTEBOOKLM-GUIDE.md`](NOTEBOOKLM-GUIDE.md) |

## What is in here

```
lessons/     185 day files, grouped by phase. Each one is a full session:
             theory, a hands-on lab, a definition of done, and recall questions.
answers/     Answer keys, one per phase. Use them to mark your own work.
slides/      The slide decks used in the recorded sessions, as standalone HTML.
             Open in any browser, navigate with the arrow keys.
curriculum.md  The 180-day map, phase by phase.
```

## The 7 phases

| Phase | Days | Covers | Cert anchor |
|---|---|---|---|
| 0 · Foundations | 0a–0e | What an OS is, the terminal, files, paths, users and root | none |
| 1 · Linux, Networking, Bash | 1–30 | Boot and systemd, CLI, permissions, text processing, networking, Bash, Python basics | RHCSA |
| 2 · Git, CI/CD, Docker | 31–60 | Git, Python for ops, pipelines, containers, Ansible | none |
| 3 · Kubernetes, Terraform | 61–90 | Kubernetes, Helm, Kustomize, Terraform | CKA |
| 4 · AWS and FinOps | 91–120 | AWS in depth, mapping to other clouds, cost engineering | AWS SAA, Terraform Associate |
| 5 · SRE and Observability | 121–150 | SLOs, monitoring, GitOps, service mesh, chaos, platform engineering | none |
| 6 · MLOps and LLMOps | 151–180 | ML pipelines, model serving, LLM systems, AIOps, capstone | none |

Every day divisible by 7 is a review day. No new material, just a cumulative quiz,
redoing your weakest lab, and catching up. Those days are not padding. They are where
the retention actually happens.

## The lab you will need

One Linux machine you are allowed to break. Any of these work:

- A VM on your laptop (VirtualBox, UTM, VMware, or `virt-manager` on Linux)
- A cheap cloud instance
- WSL2 on Windows for the early phases, though you will want a real VM by Phase 1

The recorded sessions use **CentOS Stream 9**, so `dnf` and `systemd` commands match
exactly. Rocky Linux, AlmaLinux or RHEL behave the same. On Ubuntu or Debian, swap
`dnf` for `apt` and most of the rest carries over.

From Day 1 you build one project incrementally at `/opt/fresher_project`. It starts as
a single shell script that logs boot information and grows across all six phases into
something with services, containers, pipelines, monitoring and a model behind it. Do
not skip the project steps. They are the thread that connects the days.

## How a lesson is laid out

Every day file follows the same shape, so you always know where you are:

1. **Recall warm-up**, a few questions from previous days
2. **Theory**, usually about an hour of reading, in lettered sections
3. **Lab**, about two hours, numbered and runnable
4. **Project step**, where that day moves the incremental project forward
5. **Definition of done**, the honest check on whether you actually got it
6. **Recall questions**, written for spaced repetition
7. **Next-day preview**

The recall questions at the bottom are not decoration. Feeding them into a spaced
repetition cycle is the single highest-return habit in this whole curriculum. See
[`NOTEBOOKLM-GUIDE.md`](NOTEBOOKLM-GUIDE.md).

## Recorded sessions

Videos are released as they are recorded. The written lesson for a day is usually here
before the video is.

### Phase 0 · Foundations

| Day | Lesson | Video |
|---|---|---|
| 0a | What a computer and an OS actually are | [watch](https://youtu.be/34RsHl-DaPs) |
| 0b | Your first terminal: shell, commands, man | [watch](https://youtu.be/oAz6v4k2fAE) |
| 0c | Everything is a file: the filesystem | [watch](https://youtu.be/ZChBzmA6X9E) |
| 0d | Absolute vs relative paths | [watch](https://youtu.be/L-6Tv-pk_Wc) |
| 0e | Users, root and sudo | [watch](https://youtu.be/x6bpa_CTXWU) |

### Phase 1 · Linux, Networking, Bash

| Day | Lesson | Video |
|---|---|---|
| 1 | Boot process and systemd | [watch](https://youtu.be/_TyWNPZflKk) |
| 2 | Core CLI, FHS, I/O redirection | [watch](https://youtu.be/hotNDMmfwHA) |
| 3 | Users, groups, passwd, shadow, sudoers | [watch](https://youtu.be/M5r-l7Peips) |
| 4 | Permissions deep dive: octal, SUID, ACLs | released soon |
| 5+ | Written lessons available now in `lessons/` | in production |

## Using this without the videos

That is the intended path for most people. [`SELF-STUDY-GUIDE.md`](SELF-STUDY-GUIDE.md)
covers pacing, how to actually do a lab, how to mark yourself, and what to do when you
get stuck. The short version: read the theory once, do the lab without looking at the
answers, then check. Do not read the answer key first. You will feel like you learned
it and you will not have.

## Contributing and corrections

Found a command that does not work on your distro, a broken explanation, or a typo?
Open an issue or a pull request. Corrections from people actually working through the
material are the most useful thing this repo can receive.

If a lab fails for you, include your distro and version, the exact command, and the
full error. That is enough to fix it for everyone behind you.

## License

Course content is released under [CC BY 4.0](LICENSE). Use it, remix it, teach from it.
Attribution to the Zero to LLMOps channel is all that is asked.
