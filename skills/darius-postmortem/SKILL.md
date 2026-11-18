---
name: darius-postmortem
description: "Postmortem: blameless write-up of an incident (failed pipeline run, outage, data problem, near miss) with timeline, demonstrated cause and follow-up tasks. Use when I ask for one after an incident."
disable-model-invocation: true
---

Adapted from wshobson/agents (MIT, Copyright (c) 2024 Seth Hobson), `postmortem-writing`.

A postmortem explains which conditions let the failure happen, so the fix removes the condition and not only the symptom. It describes systems and decisions, never who was at fault.

## When it is worth one

An outage or failed run that someone outside the team noticed; data lost, corrupted or sent to the wrong place; a near miss that would have been serious; a failure of a kind not seen before; or the same small failure for the third time.

## Steps

1. Collect the evidence before writing: logs, job history, the commits and deploys around the time, alerts, and what I observed. Work from metadata, timestamps and exception types; no client data in the document.
2. Build the timeline from the evidence. Mark each entry as from a log, from me, or assumed.
3. Find the cause by asking "why" until the answer is a condition that can be changed (a missing check, an unhandled case, an assumption that stopped holding). Each answer needs its evidence; an answer without evidence is written as a hypothesis. If the cause is in code and not yet demonstrated, use the `darius-root-cause` skill first.
4. Write the actions: each one names what changes, whether it prevents, detects or limits the failure, and how its success is checked. Leave out actions nobody will do.
5. Show me the draft and wait for my OK. Then save it as `POSTMORTEM-<date>-<short-name>.md` in the project's `notes/` unless I name another place, and propose each accepted action as a backlog task.

Done when the file is saved, every action is either a backlog task or explicitly dropped, and the "Not verified" section is filled in.

## Template

```markdown
# Postmortem: short name

Date: YYYY-MM-DD | Duration: ... | Impact: who or what was affected, measured where possible

## What happened
Three or four sentences a manager can read.

## Timeline
- HH:MM  event (source: log | observed | assumed)

## Cause
The chain of "why", each step with its evidence. The condition that allowed the failure.

## What went well
What limited the damage or shortened the incident.

## Actions
| Action | Type (prevent / detect / limit) | How we check it worked | Task |

## Not verified
What is still a hypothesis, and what evidence is missing.
```

## Rules

- For a small incident, "What happened", "Cause" and "Actions" are enough: half a page.
- In text for clients or management, report measured values with their caveats and do not promise that it cannot recur.
- A retry, a longer timeout or a broader `except` is recorded as a mitigation, not as the fix.
