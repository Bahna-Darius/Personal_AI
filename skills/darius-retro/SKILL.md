---
name: darius-retro
description: "Retrospective on a work session. Proposes improvements to the agent environment (agents, skills, CLAUDE.md, checks, memory)."
disable-model-invocation: true
---

Adapted from mattpocock/skills (MIT), `retro`.

Find what would make the next session cheaper or more correct. This skill only proposes: it changes no file.

## Steps

1. Load the `darius-writing-for-agents` skill; every proposal follows it.
2. Gather the sources. Use the session I name, or the current one. For orchestrated work read `notes/tasks/BACKLOG.md` and the Result and Verdict sections of the briefs involved, not the code.
3. Check every category below and keep the findings that have evidence in the sources.
4. Present at most 7 findings, most severe first, as a table: finding | evidence | proposed change | file | cost of the change. Then wait: I approve each item separately.

## Categories

- **Navigation.** The agent took long to find the right file. Propose a pointer in the project's CLAUDE.md or code map.
- **Automated checks.** A mistake that a test, linter or type check could have caught. Read the project's own check commands first (`pyproject.toml`, the commands in CLAUDE.md, CI config): a check that exists but is not run is the finding.
- **Review rules.** The reviewer agent missed something. A mechanical pattern becomes a deterministic check; a judgement call becomes a rule in the reviewer agent's definition.
- **Steering files.** A CLAUDE.md that has grown: lines that are stale, duplicated, or that change nothing in the agent's behaviour. Propose the deletion.
- **Model and effort.** An agent or chat that ran on a more expensive model than its kind of work needs, or a cheap one that had to be redone.
- **Tool economy.** An expensive or repeated tool call that a narrower read, a script or an index would replace.
- **Missing information.** Something the agent needed and could not see (a log, an output, a doc). Propose how to make it reachable without exposing confidential data.
- **Repeated work.** A procedure done by hand about three times: propose an agent (with model, effort and tools) or a skill. An agent that no task has used since its task closed: propose removing it.
- **Memory.** A project memory that holds in every project: propose moving it to the global CLAUDE.md. A memory that is no longer true: propose deleting it.

## Where rules belong

- CLAUDE.md is loaded by every agent: keep it to commands, core areas, traps and pointers.
- Standards are enforced by the reviewer agent, which receives a diff and has the context to spare; the implementing agent is the one under context pressure.
- Before proposing a new doc, check whether one already covers it.
