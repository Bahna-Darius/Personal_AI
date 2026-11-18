---
name: darius-brief
description: "Task brief (notes/tasks/T-NNN.md): write one so the feature chat can finish without asking and the verifier can check it. Use whenever the orchestrator writes or rewrites a brief."
---

A brief is complete when a feature chat that has read nothing else can do the task, and a verifier can tell done from not done. Most re-verification rounds so far came from three gaps: edge cases nobody named, tests that already failed on HEAD, and docs left saying the old thing. This skill closes them before the work starts.

## Steps

1. Use the project's brief template if `notes/tasks/` has one; otherwise the template in the global CLAUDE.md, section 3. Keep its headings and language; add the fields below inside "What" and "Done means".
2. The orchestrator reads no code and runs no suite: send a Haiku subagent to look up the facts and run the project's test command on HEAD. It returns, in at most 20 lines, the files involved, the current behaviour in five lines, and the names of the tests that already fail. Ask me only for intent, priorities and trade-offs.
3. Fill every field. A field that does not apply says "none" and why; never leave one out.
4. Show me the brief only if the task is in a core area or changes behaviour I would notice; otherwise write it and log the decision.

Done when every field below is filled and the subagent has run the verification command once on HEAD.

## Fields

In "What":
- **Goal**: the outcome I want, in one sentence, separate from the problem.
- **Current state**: what happens now, measured where possible (a count, an output, a failing case).
- **Context**: the ONE section of `docs/DECISIONS.md` to read; no more.
- **Constraints**: `Use:` existing helpers, patterns, libraries. `Never:` what must not change or be added. `May:` freedoms the chat would otherwise ask about.
- **Files the task may change**: a list; the feature chat stops and asks before touching any other file.

In "Done means":
- **Edge cases the tests must cover**: each one named (empty input, the boundary value, the case from the bug report, the false positive to avoid). Name each by shape with invented values, never a value from project data. For a parser or validator, also list the near-miss inputs that must be rejected (other separators or currencies, line breaks, labels inside words).
- **Known failing tests on HEAD**: names, or "none"; the verifier treats only new failures as regressions.
- **Docs that must change**: each file and what it must say afterwards (`docs/DECISIONS.md`, README, the project CLAUDE.md, any other doc the project keeps), or "none".
- **Exact verification command**: copy-pasteable, PowerShell syntax; run it as written with the PowerShell tool or `powershell.exe -NoProfile -Command`, never as a Bash rewrite.

## Keeping the brief short

- What stays at most 15 lines; long context goes behind a pointer.
- Re-verification rounds go below Verdict as "Round N" with only the delta, never a rewrite of earlier rounds.
