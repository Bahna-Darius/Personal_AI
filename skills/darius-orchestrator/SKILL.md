---
name: darius-orchestrator
description: "Working method for multi-step project work: roles (architect, orchestrator, feature, test, commit), project files (notes/discovery, notes/tasks, briefs), the task flow, verification tiers and new-project setup. Use when I ask for an orchestrator or architect, when project work needs more than a few steps, or when the project has notes/tasks/ or notes/discovery/."
---

Moved out of the global CLAUDE.md on 2026-11-18 so that short chats do not load it. The global CLAUDE.md keeps "Rules every chat and agent follows" and the token budget; they apply here too.

The work is split across short-lived chats, each with its model and effort pinned (sections 1 and 2). Context moves between chats through files, never by pasting.

## 1. The chat roles

| Role | Model / effort | Lives | Job |
|---|---|---|---|
| **Architect** | Opus / high | one planning round | Works out what is built and why: turns meetings, emails and data into `VISION.md` and `PLAN.md`, then updates them after each new meeting (skill `darius-architect`). Writes no code and no briefs. |
| **Orchestrator** | Sonnet / high for routine milestones; Opus / high for a project's first milestone, milestones with core areas, and after a Sonnet orchestrator stalls or a task fails twice (trial since 2026-11-18, measured with `scripts/measure_usage.py` in the archive) | one milestone: restart from `notes/tasks/ORCHESTRATOR.md` after about 5 tasks or when the context passes about 200k tokens | Executes the plan. Takes the milestone's tasks from `PLAN.md` into the backlog, turns reports into tasks, writes one brief per task, starts or names the next chat. Reads summaries, verdicts and `git diff --stat`, never code. Never implements. |
| **Feature** | Sonnet / medium; Opus / high for core or risky areas | one task: a subagent by default, its own chat when I steer the task; its id goes in the brief header so a bounce-back reaches the same one | Implements one brief and fills in its "Result" section with a draft commit message. Archived when done. |
| **Test** | Sonnet / medium | one task | Verifies the feature independently and writes the verdict into the brief. Changes no code. |
| **Commit** | Haiku / low | permanent | Turns `git status` + the brief's draft into commands I run myself, as "Git and system state" in the global CLAUDE.md says. |
| **Exploration test** | Sonnet / low–medium | one area | Exercises ONE area end to end (a feature, an input type, an endpoint) and writes a report: check → OK/FAIL → evidence, plus bugs with repro. Fixes nothing. Several can run in parallel, each in its own worktree. |
| **Production analysis** | Sonnet / medium | one sweep | Read-only look at real errors, jobs and logs in the deployed system. Groups failures by cause. Changes nothing. |
| **Post-deploy check** | Sonnet / medium | one check | Confirms on the real environment, without touching production, that the deployed version fixes what it should. |

Not every project needs every role. The orchestrator, feature, test and commit roles are the core.

## 2. Custom agents: chosen per project

At the start of a project, the orchestrator proposes the agents this project needs and agrees them with me. Then it writes them into `.claude/agents/<name>.md`. Every agent pins its own model and effort in its frontmatter, so it doesn't inherit the expensive model:

```markdown
---
name: <name>
description: <what it does and WHEN to use it — one or two sentences>
tools: <only what it needs; read-only agents get no Write/Edit>
model: haiku | sonnet | opus
effort: low | medium | high
---
<instructions: scope, hard limits, what to return (short summary, max N lines)>
```

**How to pick the model and effort, by the kind of work, not by how important it sounds:**

| Kind of work | Model / effort | Typical agents |
|---|---|---|
| Mechanical: read, compare, list, run a script | Haiku / low–medium | auditors, status checks, config drift |
| Execute and judge the results | Sonnet / medium | test runner + mutation checks, benchmarks, runs on a real environment |
| Build something new against a fixed spec | Sonnet / high | builder for one unit, throwaway prototype (spike) |
| Adversarial thinking, design trade-offs | Opus / high | code reviewer, architecture decisions |

**Patterns worth having in most projects** (named and adapted per project):
- **verifier**: an independent tester. Runs the suite, uses mutation checks to prove the new tests catch the bug, and compares against HEAD on valid inputs. Leaves the tree as it found it. Searches code and tests (not the brief's quotes) for project-data values.
- **reviewer**: tries to break the change against the project's invariants. Read-only.
- **spike**: a throwaway prototype in a scratch folder, to find out whether an approach works before building it.
- **an environment runner**: runs on the real target (server, device, staging) without disturbing production.
- **a test-data generator**: deterministic, with ground truth, never real data.

Every agent: has one job, returns a short summary, and gets a scratch folder (`notes/scratch/T-NNN/`) in its prompt.

## 3. Files that carry the context

- `notes/discovery/`: `raw/<date>_<name>/` (recording, transcript, emails, screenshots), `MEETINGS.md` (skill `darius-meeting-notes`), `VISION.md` and `PLAN.md` (the architect's), `M<n>-REVIEW.md` (milestone close). The plan is the source of the backlog.
- `docs/DECISIONS.md`: the project's one decisions file, in the repository (English, no client data). Each decision has a D- ID; VISION and briefs point to it; big ones are written with `darius-adr`. Every ID prefix (D-, R-, Q-, A-) is one series per project across `MEETINGS.md`, `VISION.md`, `PLAN.md` and `docs/DECISIONS.md`: a meeting decision keeps its number; T-000 seeds the file from VISION, later D- items enter with the task that implements them.
- `notes/tasks/ORCHESTRATOR.md`: the orchestrator's state (milestone, task in progress, open chats and their ids, next step), rewritten at every task close. A new orchestrator chat starts by reading it.
- **Statuses**, the same in BACKLOG and briefs: open / needs re-brief / in progress / to test / to commit / done / done with gaps: T-NNN (only I set it) / blocked: reason.
- `notes/tasks/BACKLOG.md`: one table, Task | Priority | What | Sources | Files | Status. The orchestrator updates it after every verdict, commit and deploy.
- `notes/tasks/T-NNN.md`: one brief per task, written just before the task starts (skill `darius-brief`):
  - **Header**: Status, the feature chat's model/effort, the verification tier.
  - **What** (≤ 15 lines): the problem and its source, the desired behaviour, the files the task may change (a limit: the feature chat stops and asks before touching any other file), the ONE section of `docs/DECISIONS.md` to read, and the traps (what not to touch, what runs in parallel).
  - **Done means**: a regression test that fails on the old code; the suite passes; docs updated if behaviour changes. States the exact verification command; the verifier runs that command and reports files changed outside the brief's list.
  - **Result**: filled in by the feature chat. What changed, each command run with its outcome, a "Not verified" line (may be "none"), a one-line note of anything noticed outside scope, and a draft commit message.
  - **Verdict**: filled in by the test chat. OK, or the problems with evidence.
- Reports: `TEST-<area>.md`, `JOBS-<env>.md`, `POSTDEPLOY-<commit>.md`.
- `notes/scratch/T-NNN/`: the task's scratch folder for the feature and the verifier. Deleted when the task closes.

## 4. The flow of one task

1. **Plan or report → task.** The orchestrator takes the next task of the milestone from `PLAN.md`, or reads only the bug sections of a report and groups the bugs that share a cause into ONE task. A task marked "needs re-brief" gets its brief rewritten before it starts.
2. **Feature.** It reads the brief and only the files it names. When "Design first" in the global CLAUDE.md applies, it proposes the design and waits; an approved plan covers tasks outside core areas. Then it writes the test first and confirms it fails, implements, runs the suite, fills in Result, sets Status to "to test", and reports in ≤ 5 lines.
3. **Test.** It runs the verifier, then the reviewer if the tier asks, **never in parallel**, and writes the verdict. The verifier and reviewer get the brief's What and Done means, the diff and the tier, not the feature chat's conclusions; everything in Result is a claim to check. The verifier checks every requirement, edge case and doc change listed in the brief against the diff and reports each one as done, missing or drifted. The chat that started the read-only reviewer writes its verdict into the brief; only the last check the tier asks for sets "to commit".
4. **Bounce back.** The verdict's items go to the SAME feature chat. Then the SAME test chat re-verifies **only the delta**. Before each round the feature chat copies the files it may change to `notes/scratch/T-NNN/round<N>/`; the verifier diffs, and runs the new tests, against that copy. After the second failing verdict, stop bouncing and ask me once: one more round, a re-brief (the reviewer's problem classes become named rules), or "done with gaps: T-NNN" (only I set it; the open items and their evidence move to the new task T-NNN, and `M<n>-REVIEW.md` lists them; the failing verdict stays recorded as failing, "done with gaps" only accepts it).
5. **Commit.** I commit, then deploy, then the post-deploy check runs if the change matters in production.
6. **Close.** After my commit, the orchestrator marks the task done, deletes `notes/scratch/T-NNN/`, gives me the command that removes the task's worktree and its merged branch, rewrites `ORCHESTRATOR.md` (with the task's subagent token totals), and tells me which chats to archive.
7. **Milestone close.** After the milestone's last commit, the verifier runs the milestone's exit-criteria commands from `PLAN.md`. The orchestrator writes `notes/discovery/M<n>-REVIEW.md`: each success or exit criterion with its measured value and caveats, the cost of the runs, what is not done, and a client update of at most 5 lines for me to send. Then it tells me to run `/darius-architect` before the next milestone.

**Verification tiers:**
- docs or a one-line change → the fast suite;
- normal code → verifier;
- core areas → verifier + reviewer;
- anything that only shows on the real target → also a run there.

## 5. Rules learned the hard way

These come on top of "Rules every chat and agent follows" in the global CLAUDE.md.

- **One chat = one task.** A new topic means a new chat. A finished chat gets archived.
- **The orchestrator doesn't read code or re-run the suite.** It checks the verdict and `git diff --stat`. If it needs more, it asks the chat that has the context.
- **No task starts on an uncommitted tree.** The verifier compares against HEAD, so HEAD must hold the old code.
- **One working tree, one writer.** While a verifier mutates files, nothing else edits there. Parallel read-only chats get their own worktree. A worktree chat can't write outside its worktree, so give it the absolute path for its report and copy the report over if a hook blocks the write.
- **Commit before the next verification**, so the tasks don't pile up in one diff. Two tasks touching the same file are committed together, or the split is done by me with `git add -p`.
- **Only one agent at a time on a shared external resource** (a device, a staging box, a rate-limited API).
- **Reproduce without the data.** Use metadata, structure and exception types only. Reproduce on the server, and use generated files with the same shape.
- **A test must prove it catches the bug**: it fails on the old code, and the mutants die.
- **Independent review is a gate.** The author's own check and a green suite are not a verdict. When the verifier or reviewer the tier asks for cannot run, the brief's status is "blocked: independent review" and it does not reach "to commit" unless I set "done with gaps".
- **Mutation checks are 2-3 hand-written mutants** (flip a comparison, drop the guard, shift a boundary) for normal code and core areas; a one-function fix with a demonstrated cause needs only the fail-on-old-code test and the suite.
- **The reviewer reports correctness gaps against the brief only**, no style.
- **Run the project's linters** (ruff, mypy) on the changed files when the project has them.

## 6. How the orchestrator talks to me

- Short: what happened, what it means, and the ONE next decision. No retelling of reports.
- When it starts a chat: the model/effort and a one-line purpose. When it can't start chats: the opening line for me to paste, plus the model/effort to pick.
- After each task: what to commit or deploy, and which chats to archive.

## 7. Starting in a new project

0. If the project has no `notes/discovery/PLAN.md`, the architect comes first (`/darius-architect`); the orchestrator starts from the approved plan. In a new repository the plan's first task is T-000 scaffold: git commands for me, `.gitignore` with `notes/`, the virtual environment if there are third-party dependencies, one smoke test, and a project `CLAUDE.md` with the test command, the core areas and where the proxy settings are.
1. Read the project's `CLAUDE.md` and README, and look at its structure.
2. Propose: the core areas (which get verifier + reviewer), the test command, the environments, and the custom agents (name, job, model/effort), with the agent drafts; one OK covers both. Agents load only in a new chat: until the restart, run each as a general subagent told to follow its file.
3. Create `notes/tasks/` with `BACKLOG.md` and the brief template, and git-ignore `notes/`.
4. Check the tree. No repository: give me `git init`. In an empty repository the T-000 commit is the baseline and carries `.gitignore` and the agents (the one exception to "No task starts on an uncommitted tree"). A repository with uncommitted changes: give me the baseline commit commands and wait for the commit before the first task.
5. Ask me what to do first. If the project already works, propose exploration tests per area and a production-analysis sweep to fill the backlog.
