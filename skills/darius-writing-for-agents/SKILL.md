---
name: darius-writing-for-agents
description: Writing documents an agent reads. Use when creating or editing a subagent definition (.claude/agents/*.md), a skill (SKILL.md), a CLAUDE.md file or a task brief.
---

Adapted from mattpocock/skills (MIT), `writing-for-agents`.

The same levers make an agent's behaviour predictable in every document it reads: an agent definition, a skill, a CLAUDE.md, a brief.

## Pointers

A pointer is a line that stays in context and names material that is not: an agent's or skill's `description`, or a CLAUDE.md line that names a doc. The pointer's wording decides whether the agent reaches the material.

- State what the material is and the cases that should trigger it.
- Put the triggering word first. One trigger per case; collapse synonyms.
- Leave out what the body already says.
- Material the agent must reach behind a weak pointer is a bug. Sharpen the pointer first; inline the material only when that fails.

## Two loads

- **Context load**: everything always loaded costs tokens on every turn. CLAUDE.md files and every description pay it.
- **Cognitive load**: material with no pointer depends on me remembering it exists. Spend my attention only where my judgement matters.

## Hierarchy

Place each piece by how soon the agent needs it:

1. **Step** in the file: what the agent does, in order.
2. **Reference** in the file: rules and definitions consulted on demand.
3. **Disclosed reference**: a separate file behind a pointer, loaded only when the pointer fires.

Inline what every case needs; disclose what only some cases reach. Keep a concept's definition, rules and caveats under one heading. A document that is too long thins attention even when every line is true: disclose or split.

## Completion criteria

End every step on a criterion that lets the agent tell done from not done. Prefer one that is checkable and exhaustive ("every caller in the grep output is updated") over a vague one ("update the callers"). When an agent stops early, sharpen the criterion first. Hiding later steps only works across a real context boundary: a hand-off or a subagent.

## Wording

- Use a strong existing word for a behaviour and repeat that same word (verifier, read-only, dry run). Define a coined word once, or do not coin it.
- Describe the behaviour you want. Keep a prohibition for a hard limit, and pair it with what to do instead.

## Pruning

- One meaning lives in one place. Point to it; do not restate it.
- The environment is a source of truth too (`pyproject.toml`, config files, `--help`). Write down only what cannot be found by looking: conventions, reasons, traps.
- Delete a sentence the model already follows by default. Delete the whole sentence, not a few words of it.
- Delete what went stale.

## Mechanics in this setup

**Subagent** (`<project>/.claude/agents/<name>.md`; `~/.claude/agents/` only for an agent every project needs):
- Frontmatter: `name`, `description`, `tools`, `model`, `effort`. The model and effort table is in the global CLAUDE.md, section 2; choose by the kind of work.
- `tools`: only what the job needs. A read-only agent gets no Write or Edit.
- Body: one job, its scope, its hard limits, the scratch folder it may write to, and the return format (a short summary with a line limit).
- An agent created for one task is deleted or archived when the task closes; an agent for a recurring task stays.

**Skill** (`~/.claude/skills/<name>/SKILL.md`, or `<project>/.claude/skills/`):
- Frontmatter: `name`, `description`. Add `disable-model-invocation: true` to a skill that only I should start.
- Make a skill when the same procedure has been done by hand about three times and has stopped changing.

**CLAUDE.md**: loaded in every session of its scope. Keep commands, core areas, traps and pointers to docs; move long explanations to a doc behind a pointer.

**Brief** (`notes/tasks/T-NNN.md`): follow the template in the global CLAUDE.md, section 3.

## Before writing

Agents, skills, CLAUDE.md files and settings are persistent configuration: show me the draft with a one-line reason and wait for my OK.

## Check before handing over

- Does the description say what it is and when to use it, trigger first?
- Does every step end on a checkable criterion?
- Is any meaning written twice, or copied from the environment?
- Would the agent behave the same with this sentence removed? Then remove it.
