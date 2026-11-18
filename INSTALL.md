# Install on a new laptop

Requirements: Claude Code (CLI or desktop app) and Git. Nothing else is installed by this setup: no hooks, no scripts that run on their own.

## 1. Copy the files

`%USERPROFILE%` is your user folder on Windows (`~` on macOS and Linux).

| From this repository | To |
|---|---|
| `CLAUDE.md` | `%USERPROFILE%\.claude\CLAUDE.md` |
| `skills\<name>\` (each folder) | `%USERPROFILE%\.claude\skills\<name>\` |
| `agents\<name>.md` | `%USERPROFILE%\.claude\agents\<name>.md` |

If a `CLAUDE.md` already exists there, compare the two first and merge by hand.

Windows PowerShell, from the folder where this repository is cloned:

```powershell
$t = "$env:USERPROFILE\.claude"; New-Item -ItemType Directory -Force "$t\skills", "$t\agents" | Out-Null; Copy-Item .\CLAUDE.md "$t\CLAUDE.md" -Confirm; Copy-Item .\skills\* "$t\skills\" -Recurse; Copy-Item .\agents\*.md "$t\agents\"
```

## 2. Adapt three things in `CLAUDE.md`

1. **Environment**: operating system, shell (the rules assume Windows PowerShell 5.1 without `&&`), and how the network proxy works, if there is one.
2. **Data and cost**: the rule that project data may be read in a chat assumes a company-approved Claude account. Check the new employer's policy and change the rule if needed.
3. **Language**: replies in Romanian, repository files in English. Change if you like.

## 3. Check

Start a new Claude Code session and ask:

```text
List the rules you loaded from ~/.claude/CLAUDE.md, the darius-* skills you see, and the darius-* agents.
```

Then run the configuration auditor once:

```text
Use the darius-config-auditor agent to check my configuration (checks 2, 3 and 4).
```

## 4. First project

In the project folder, with Opus selected:

1. Put the first meeting's transcript and e-mails in `notes/discovery/raw/<date>_<name>/` and say "Process the meeting in that folder" (a Sonnet chat is enough).
2. `/darius-architect` to get the vision and the plan; approve them.
3. A new chat: "You are the orchestrator. Read notes/discovery/PLAN.md, notes/tasks/ORCHESTRATOR.md (if any) and notes/tasks/BACKLOG.md and continue the current milestone."

Make sure `notes/` is in `.gitignore` before anything is saved there.
