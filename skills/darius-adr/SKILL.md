---
name: darius-adr
description: "Architecture decision record: write down one significant technical decision with its context, the options rejected and the consequences. Use when I ask to record a decision."
disable-model-invocation: true
---

Adapted from wshobson/agents (MIT, Copyright (c) 2024 Seth Hobson), `architecture-decision-records`.

An ADR records why a decision was taken, so a later chat or colleague does not reopen it without new facts.

## When it is worth one

Write one for a choice that is costly to reverse or that shapes other work: a framework or database, an API contract, a data model, a security or deployment approach, a core-area design. Skip it for bug fixes, version bumps, config values and implementation details; those belong in the commit message or the brief.

## Steps

1. Decisions live in `docs/DECISIONS.md` (darius-orchestrator skill, section 3). If the project already keeps them elsewhere (an `adr` folder, another decisions file), use that place and do not create a second one.
2. Take the next D- number free in both `notes/discovery/MEETINGS.md` and `docs/DECISIONS.md` (one series). In `docs/DECISIONS.md` the ADR is a section headed `D-NN. Title`; in an `adr` folder it is a file `NNNN-short-title.md`.
3. Draft from the conversation and the code. Ask me only for what cannot be found: the constraint that forced the choice, or which option I rejected and why.
4. Show me the draft and wait for my OK before writing the file.
5. Add the ADR to the folder's index if it has one. If this decision replaces an older one, set the old ADR's status to "Superseded by NNNN" and change nothing else in it.

Done when the file exists, the index lists it and any superseded ADR points to it.

## Template

```markdown
# NNNN. Title as a decision ("Use X for Y")

Status: Proposed | Accepted | Superseded by NNNN | Rejected
Date: YYYY-MM-DD

## Context
The problem, the constraints and the facts that forced a decision. Measured values with their caveats.

## Decision
What was chosen, in one or two sentences.

## Options considered
One line each: the option, and why it was not chosen.

## Consequences
What becomes easier, what becomes harder, what it costs, and what would make us revisit this.
```

## Rules

- At most one page. One decision per ADR.
- An accepted ADR is never edited apart from its status line; a change of mind is a new ADR that supersedes it.
- State the downsides of the chosen option plainly.
- An ADR that goes into Git follows the project's repository rules: English, no names of people, no client data, no internal ids.
