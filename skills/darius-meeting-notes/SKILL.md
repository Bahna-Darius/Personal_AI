---
name: darius-meeting-notes
description: "Meeting folder (my .mp4 recording, its transcript, the emails I downloaded): extract decisions, requirements, numbers, open questions and action items into notes/discovery/MEETINGS.md, each tagged STATED or INFERRED. Use when I give a meeting folder, recording or transcript, or ask what was decided in a meeting."
---

The goal is a record of the meeting that can be trusted later: nothing invented, nothing silently resolved, every item traceable to who said it.

## Steps

1. If the project is a git repository, check that `notes/` is git-ignored (`git check-ignore -q notes/discovery`). If it is not, stop and tell me before anything else, so no recording or email can be committed.
2. Open the meeting folder: `notes/discovery/raw/YYYY-MM-DD_short-name/` (or the path I give). I record every meeting as `.mp4` and put my downloaded emails in the same folder. Expected content:
   - `*.mp4`: the recording. You cannot hear or watch it here (no ffmpeg or speech-to-text on this laptop, and I install nothing). Never claim to have watched it.
   - the transcript: `.docx` from Word for the web (Transcribe, upload the `.mp4`) or `.vtt`/`.docx` from Teams. If there is only the `.mp4`, stop and tell me these two ways to get one, then wait.
   - emails as `.pdf`, `.txt`, `.html` or `.eml`. A `.msg` file cannot be read here: list it and ask me to save it again as PDF or text.
   - optional `.png` screenshots I took of what was shown on screen; read them, and cite them by file name.
   Read `.docx` with the `anthropic-skills:docx` skill and `.pdf` with the Read tool (or `anthropic-skills:pdf`). Read a long transcript in ranges, not in one piece. Emails go into the same section: decisions, requirements and numbers from an email cite the email (sender role, date, subject) instead of a timestamp.
3. Read the existing `notes/discovery/MEETINGS.md`, if any, to continue the ID numbering and to spot contradictions. Every ID prefix (D-, R-, Q-, A-) is one series per project: take the next number free in `MEETINGS.md`, `VISION.md`, `PLAN.md` and `docs/DECISIONS.md`. Create the file with a one-line header if it does not exist.
4. Append one section for this meeting, using the template below. Write in the language the project's notes already use (English if none); quotes stay in the original language, German terms stay German.
5. Check every line before reporting: quotes verbatim, a tag on every D- and R- item, "Blocks" is exactly one word, and any claim about a file you did not open in this run is marked INFERRED. Then report to me in at most 15 lines: counts per heading, every open question, every contradiction, and the items you were least sure about. Ask me to review the section; change nothing else. End with the next step: after the project's first meeting, or when a decision or requirement changed, I open a new Opus chat and run `/darius-architect`.

Done when the section is appended, every item has an ID and a tag, and the report is sent.

## Tagging rules

- **STATED**: said in the meeting or written in an email. Give the speaker's role (or name), the timestamp if the transcript has one, and a quote of at most one sentence. The timestamp is also my pointer into the `.mp4`: where something was shown on screen and not said, write "see video hh:mm:ss" so I can look it up.
- **INFERRED**: your reading of what was said. Say what it is inferred from. An inferred item is never a requirement or a decision until I confirm it.
- Anything unclear, disputed or left open goes to Open questions. Never answer an open question yourself.
- Data samples and reference sheets show how the work is done today. Their formats and habits are neither requirements nor open questions unless someone said so.
- A later written source supersedes an earlier spoken one: mark the old item "SUPERSEDED by <ID>, pending my OK", list the pair under Contradictions, and name decisions that were only spoken.
- A number keeps its unit, its context and whether it was exact or approximate.
- If the transcript quality is poor in a passage (missing speakers, garbled words), say so in the header instead of guessing.

## Template

```markdown
## YYYY-MM-DD: meeting title
Source: notes/discovery/raw/<file> | Attendees (roles): ... | Transcript quality: good / partial / poor

### Decisions
- D-nn: decision. STATED (role, hh:mm, "quote")

### Requirements
- R-nn: requirement. STATED (...) | INFERRED (from ...)

### Numbers mentioned
- value and unit, context, who said it, exact / approximate

### Open questions
- Q-nn: question. Owner: role. Blocks: planning / task / nothing.

### Action items
- A-nn: action. Owner: role. Due: date or "not set".

### Risks
- ...

### Contradictions (earlier meetings or this meeting's sources)
- R-nn now vs R-mm on YYYY-MM-DD: ...
```

## Data

Transcripts, emails and their content stay in `notes/` (git-ignored). Never copy them into code, tests, commits or repository documents.
