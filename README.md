# Personal AI: a working method for Claude Code

This repository is my complete, reusable setup for working with Claude Code as a team of agents: the global rules, the skills and the agents, plus the explanation of how they fit together. It contains no company data, no project memory and no internal addresses, so it can move with me to any laptop or employer.

Install it with [INSTALL.md](INSTALL.md).

## The idea in one paragraph

I do not write long prompts. Work moves through files: meetings become structured notes, notes become a vision and a plan, the plan becomes small tasks, and each task is done by a short-lived chat or subagent with the cheapest model that can do it. Every change is checked by someone other than its author, and I keep the decisions that matter: what to build, the design of risky parts, and every commit.

## Principles

- **I control Git, system changes and anything that costs money.** Claude gives me the exact commands.
- **Design first** for anything that changes behaviour, a signature, several files or a core area; an approved plan counts as that approval for routine tasks.
- **Context moves through files, never by pasting.** A chat can be restarted at any time from the files it left behind.
- **Independent verification is a gate.** A green test suite written by the author is not a verdict.
- **Cheapest model that can do the job.** Opus plans and reviews, Sonnet builds and verifies, Haiku does mechanical checks.
- **Project data stays in the project's `notes/` folder**, out of Git, out of code and tests.
- **Measured values with caveats**, never estimates presented as promises.

## Roles

| Role | Model | Lives | Job |
|---|---|---|---|
| Architect | Opus / high | one planning round | Turns meetings, e-mails and data into `VISION.md` and `PLAN.md`; updates them after each meeting. Writes no code. |
| Orchestrator | Opus / high | one milestone (restarts from `ORCHESTRATOR.md`) | Takes tasks from the plan, writes briefs, starts the other agents, reads only summaries and verdicts. |
| Feature | Sonnet / medium | one task | Implements one brief, test first. |
| Verifier | Sonnet / medium | one task | Runs the suite, proves the new test catches the bug with hand-written mutants, compares with the old version, checks every requirement in the brief. |
| Reviewer | Opus / high | one task, core areas only | Tries to break the change; reports correctness gaps only. |
| Commit | Haiku / low | permanent | Turns the result into commit commands I run myself. |

## From the first meeting to working code

1. **Meeting.** I record it, get a transcript (Teams, or Word for the web → Transcribe), and save the downloaded e-mails as PDF or text in `notes/discovery/raw/<date>_<name>/`.
2. **Meeting notes** (`darius-meeting-notes`, Sonnet). Decisions, requirements, numbers, open questions and action items go into `MEETINGS.md`, each tagged STATED (who, when, quote) or INFERRED. Open questions are never answered by the model; a later written source supersedes a spoken one only with my OK.
3. **Plan** (`/darius-architect`, Opus). An interview on what is unclear, then `VISION.md` (goal, success criteria, scope, constraints) and `PLAN.md` (architecture, milestones with runnable exit criteria, tasks with a verification command each). A gate stops planning while a blocking question is open. I approve both.
4. **Execution** (orchestrator, Opus). Per task: a brief (`darius-brief`) with goal, current state, constraints, the files it may change, edge cases with invented values, the tests that already fail, the docs to update and the exact verification command. Then feature → verifier → reviewer for core areas → my commit.
5. **When verification fails twice**, the orchestrator stops and asks me once: one more round, a re-brief, or "done with gaps" (only I can set it; the gaps become a new task).
6. **Milestone close.** The verifier measures the exit criteria; the orchestrator writes `M<n>-REVIEW.md` with measured values, cost, what is not done, and a five-line client update for me to send.
7. **A new meeting** repeats steps 1 and 2; the architect updates the plan in place, marks affected tasks "needs re-brief", and waits for my OK.

## Files in a project

```
notes/                      git-ignored
  discovery/
    raw/<date>_<name>/      recording, transcript, e-mails, screenshots
    MEETINGS.md             what was said and decided, with IDs
    VISION.md  PLAN.md      the architect's
    M<n>-REVIEW.md          milestone close
  tasks/
    BACKLOG.md              one row per task, one status list
    ORCHESTRATOR.md         the orchestrator's state, for restarts
    T-NNN.md                one brief per task
  scratch/T-NNN/            temporary files of a task
docs/DECISIONS.md           decisions, in the repository
```

IDs (D- decisions, R- requirements, Q- questions, A- actions) are one series per project across all these files.

## Skills

| Skill | Starts | Use |
|---|---|---|
| `darius-meeting-notes` | by itself, when given a meeting folder | transcript and e-mails into `MEETINGS.md` |
| `darius-architect` | `/darius-architect` | vision, plan, updates after meetings |
| `darius-brief` | by itself, when a brief is written | complete task briefs |
| `darius-root-cause` | by itself, on a bug or failing test | demonstrated cause before any fix; stops after three failed fixes |
| `darius-grill-me` | `/darius-grill-me` | interview me until a plan has no open decisions |
| `darius-adr` | `/darius-adr` | record one important decision |
| `darius-postmortem` | `/darius-postmortem` | blameless incident write-up with follow-up tasks |
| `darius-retro` | `/darius-retro` | look back at a session and propose improvements |
| `darius-writing-for-agents` | by itself | how to write agents, skills, CLAUDE.md files and briefs |

## Agents

- `darius-researcher` (Sonnet): one research question on the web, official sources first, judged against this setup.
- `darius-config-auditor` (Haiku): read-only check of the configuration: drift, broken references, contradictions, size of always-loaded text.

## Keeping cost down

Measured over 30 days of real use: long sessions re-reading their own history are the main cost; whole-file reads and shell dumps come next; test output is under 3 %. Hence: chats live for one task (the orchestrator for one milestone), large files are read by range, tests run inside subagents with only the last lines returned, and the model is never switched in the middle of a task.

## How it was tested

Two end-to-end runs on an invented project (an invoice-triage proof of concept with planted traps: a meeting-versus-e-mail contradiction, an unreadable e-mail file, an unwatched recording, a credit note, a duplicate, a missing due date). The first run found 39 problems that collapsed into 9 root causes; after the fixes, the second run passed 7 of 9 checks, one partly failed and one could not be exercised, and its owner stops dropped from 22 to 12 (the second run had one stage fewer). Five further small fixes followed and have not been re-tested yet.

## Adapting it to a new place

Edit the "Environment" section of `CLAUDE.md` (operating system, shell, proxy), check the "Data and cost" rule against the new employer's policy, and keep the rest. See [INSTALL.md](INSTALL.md).
