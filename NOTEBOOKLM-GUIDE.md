# Using NotebookLM as Your Tutor

Turn this repo into a personal tutor that quizzes you, explains what you got wrong,
and cites the exact lesson it came from.

[NotebookLM](https://notebooklm.google.com) is free. It only answers from sources you
give it, so unlike a general chatbot it will not invent a command that does not exist,
and every answer points back at the file it came from. That property is why it suits
this curriculum: in ops, a confidently wrong command costs you a production system.

This is the same method used to teach the course. It is written here so you can run it
alone.

---

## One-time setup, about 20 minutes per phase

Build **one notebook per phase**, not per day. Per-day notebooks lose the connections
between days, which is where most of the understanding lives.

1. Download this repo. Either `git clone` it or use the green **Code → Download ZIP**
   button on GitHub.
2. Go to [notebooklm.google.com](https://notebooklm.google.com) and create a notebook.
   Name it for the phase, for example `Zero to LLMOps P1 — Linux (D1–30)`.
3. **Add source → File upload**, and upload:
   - Every day file for that phase, **individually**, not zipped and not merged.
     For Phase 1 that is `day-001.md` through `day-030.md`. Uploading them separately
     is what makes citations point at a specific day, which matters a lot when you are
     asking "where did I learn this?"
   - The matching answer key, `answers/answers-phase-1.md`.
   - `curriculum.md`, so questions about what is coming next work.
4. Add the official documentation for that phase's tools as **website** sources. For
   Phase 1, the RHEL or Rocky Linux docs. For Phase 2, `git-scm.com/docs` and
   `docs.docker.com`. For Phase 3, `kubernetes.io/docs`. This gives the notebook a
   verification layer beyond the course material.

A notebook holds 50 sources, so a phase of 30 lessons plus keys and docs fits
comfortably.

Repeat for each phase as you reach it. Do not build all seven up front.

## Ritual A: before you start a new day, about 10 minutes

Before reading Day N, test yourself on what it assumes you already know. Paste this,
replacing `N`:

> I am a learner about to study Day N. Before I start, quiz me on the prerequisite
> material it builds on. Ask me the recall warm-up questions from day-0NN one at a
> time. Do not show me any answers until I attempt each one. After each attempt, grade
> me strictly against the answer key and the day files, and correct anything I got
> wrong or left incomplete.

Answer from memory, out loud if you can. Where you were wrong:

> Explain <the topic I got wrong> from scratch, as if I have never seen it, using only
> the course sources. Then give me one command I can run to prove it to myself.

That last clause matters. An explanation you can verify in a terminal sticks. One you
only read does not.

## Ritual B: every study session, about 10 minutes

This is the spaced repetition cycle, and it is the highest-return habit in the whole
curriculum. Material reviewed at widening intervals moves into long-term memory.
Material met once and never revisited is mostly gone inside a month.

Review at **1, 3, 7 and 21 days**. Paste this at the start of each session, with today
as Day N:

> Give me a mixed 8-question rapid-fire quiz drawn only from Days N-1, N-3, N-7 and
> N-21, skipping any that do not exist yet. Ask one question at a time and do not give
> answers until I attempt. Weight it toward the recall-question lists in those day
> files. At the end, tell me which days I was weakest on.

If a day comes back weak twice running, stop and re-read its theory section before
moving forward. That signal is reliable and worth obeying.

## Ritual C: after a lab, when something did not work

Paste the exact command and the exact error, then:

> This is from the Day N lab. Explain what this error is telling me and what I should
> check next. Do not give me the fixed command yet. Point me at the concept I have
> misunderstood.

Ask for the diagnosis before the fix. Diagnosis is the actual job. If you always ask
for the fix, you end up with a course's worth of working commands and no ability to
handle the first failure you meet that is not in the course.

Once you have solved it yourself, then ask:

> Now show me the correct command and explain why mine failed.

## Ritual D: review days

On every day divisible by 7:

> Generate a cumulative 10-question quiz covering Days N-6 to N. Mix conceptual
> questions with "what command would you run" questions. Ask them one at a time,
> no answers until I attempt, then grade me strictly and cite the day file for each.

Follow it with:

> Based on how I just did, tell me which single lab from this week I should redo, and
> why.

Then actually redo that lab, from a clean state.

## Other features worth using

- **Study guides and flashcards.** Generate these per week rather than per day. Per
  day they come out shallow because there is not enough material to work with.
- **Audio Overview.** Generate one for the coming week's lessons and listen on a
  commute. It is a decent passive first pass. It is not a substitute for doing the
  labs, and treating it as one is a trap, because listening feels like progress.
- **Mind map**, where your account has it, is genuinely good at the end of a phase for
  seeing how the pieces connect.

## Prompts worth keeping

Interview preparation, once you are a few phases in:

> Act as a DevOps interviewer. Ask me one scenario-based question at a time drawn from
> Days 1 to N. After each answer, tell me what a strong candidate would have added.

Connecting a concept across the curriculum:

> Where does <concept> appear across all the days in this notebook, and how does its
> role change as the curriculum progresses?

Honest self-assessment before moving up a phase:

> Based only on the recall questions across this whole phase, give me a 15-question
> final exam. Grade me strictly at the end and tell me plainly whether I am ready for
> the next phase or should review first.

## If you would rather not use NotebookLM

Nothing here depends on it. The same rituals work with any AI tool that lets you
attach files, and they work with no AI at all: the recall questions at the bottom of
every day file are the raw material, and index cards have taught people harder subjects
than this one.

What matters is not the tool. It is testing yourself from memory, at widening
intervals, and being honest when you get something wrong.
