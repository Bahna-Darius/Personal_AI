---
name: darius-grill-me
description: Interview me about a plan or design until every decision is settled, before any code or brief is written.
disable-model-invocation: true
---

Adapted from mattpocock/skills (MIT), `grill-me` and `grilling`.

Interview me until we share one understanding of the plan. Take no action on the plan until I confirm the summary at the end.

## The design tree

Treat the plan as a tree of decisions: each decision opens the decisions that depend on it. The **frontier** is the set of open questions whose prerequisites are already settled.

## Rounds

1. Ask the frontier, at most 5 questions per round, numbered.
2. Give every question a recommended answer with a one-line reason, and word it so that "da" accepts the recommendation.
3. Wait for my answers. Then recompute the frontier and ask the next round.
4. A question that depends on an unanswered one waits for a later round.

Ask in Romanian. When the topic is new to me, add one line on why the decision matters.

## Facts and decisions

- A **fact** is something the environment can answer: what the code does, which config exists, what a library supports. Find it yourself: read the file, or send a read-only subagent on a cheap model when the search is wide. A question that depends on a pending lookup waits for it; the rest of the frontier goes ahead.
- A **decision** is a choice between valid options. Every decision comes to me.

## Done

The interview is done when the frontier is empty and nothing is assumed silently. Then:

1. Write the decisions as a summary of at most 12 lines, with anything set aside for later on its own line.
2. Wait for my confirmation.
3. After my OK: in a project that uses `notes/tasks/`, the summary becomes the "What" section of the task brief; anywhere else, continue with the work.
