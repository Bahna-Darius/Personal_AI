---
name: darius-root-cause
description: "Bug, failing test, error or unexpected behaviour: find the demonstrated cause before changing code. Use before proposing any fix, and again when a fix has failed."
---

Adapted from obra/superpowers (MIT, Copyright (c) 2025 Jesse Vincent), `systematic-debugging`.

A fix is allowed only for a demonstrated cause: one you can switch on and off, or show in output. Until then, change no source code; adding temporary logging is fine.

## 1. Investigate

1. Read the whole error: full stack trace, warnings, file and line, exit code.
2. Reproduce it. Write down the exact command and whether it fails every time. If it does not reproduce, collect more data instead of guessing. With confidential data, reproduce on generated input of the same shape.
3. Check what changed: `git diff`, `git log -10`, dependencies, config, environment.
4. Rule out the environment before the code: the Environment section and "A refusal or quarantine isn't a crash" in the global CLAUDE.md.
5. Several components (script, API, database; client, proxy, server): log what enters and what leaves each boundary, run once, and find the first boundary where the value is wrong. Investigate only that component.
6. A wrong value deep in a stack: trace it back to where it is first created.

Done when you can name the file and line where behaviour first diverges from what is expected.

## 2. Compare

Find similar code in the project that works. Read it completely. List every difference between the working and the failing case, including config and environment.

Done when the list of differences is written down.

## 3. One hypothesis

State it: "X is the cause because Y." Test it with the smallest change, one variable at a time. If it is wrong, undo that change and form a new hypothesis; never stack a second change on a failed one.

Done when switching the one variable makes the failure appear and disappear.

## 4. Fix

1. Write the regression test and confirm it fails. An existing failing test counts as the regression test only if it also fails when run alone; otherwise write one that does.
2. Make one change at the cause. No unrelated edits.
3. Run the verification command and the full suite.

Whether to propose the design first follows "How to work with me" in the global CLAUDE.md.

**Three failed fixes: stop.** Report what each attempt changed and what it revealed, and ask me. Fixes that keep exposing new problems elsewhere point to a design problem, not a fourth patch.

## No cause found

Report what was checked and ruled out, and leave logging at the boundary where the evidence ends. A retry, timeout or broader `except` is a workaround: name it as one and wait for my OK.

## Report

Cause with its evidence; what was ruled out; the fix; the test; what was not verified.
