---
name: darius-gcloud
description: "gcloud CLI (Google Cloud): check syntax with gcloud help, keep output small, and stop before any command that changes or costs something. Use when planning, writing, explaining or running a gcloud command, or calling a Google Cloud API such as Vertex AI / Gemini with a gcloud token. Not for Google Cloud client-library code."
---

Adapted from google/skills (Apache License 2.0, see LICENSE in this folder), `skills/cloud/gcloud` v1.0.4, https://github.com/google/skills. Changed: usage-attribution tags and the remote MCP server removed; approval, environment and sign-in rules added for this setup; text shortened.

Remembered gcloud commands and flags are stale. The installed CLI is the authority: `gcloud help <leaf command>` describes exactly the version on this laptop.

## Running gcloud here

- Windows PowerShell 5.1. gcloud is on my user PATH, but the app's shell may not see it. Check once with `Get-Command gcloud`; if it is not found, call `& "$env:LOCALAPPDATA\Google\Cloud SDK\google-cloud-sdk\bin\gcloud.cmd"` for every gcloud command in the session.
- Corporate proxy with TLS inspection: when a call fails with an SSL, certificate or connection error, suspect the proxy and the CA first (`gcloud config list` shows `proxy/*` and `core/custom_ca_certs_file`). Give me the fix as a command; do not change gcloud config yourself.
- A REST call next to gcloud (e.g. Vertex AI `generateContent`) from PowerShell 5.1: keep the token in a variable (`$tok = & <gcloud> auth print-access-token`), pass `-Proxy $env:HTTPS_PROXY` to `Invoke-RestMethod` (the system proxy answers 407 Proxy Authentication Required), and set `[System.Net.ServicePointManager]::Expect100Continue = $false` before a POST (the proxy answers 417 Expectation Failed).
- I sign in myself (`gcloud auth login`, `gcloud auth application-default login`). Never print an access or identity token into the chat; never create or download a service-account key.

## Steps for every task

A plan for a gcloud task follows these steps in this order.

1. **Syntax.** Run `gcloud help <leaf command>` for the exact command (e.g. `gcloud help compute instances create`); help on a parent group does not count. Done when every flag and positional you will use appears in that output; global flags (`--project`, `--quiet`, `--format`) are not listed per command and need no check. If I pasted the help output, or told you not to run anything, skip it and say the syntax is unchecked.
2. **Scope.** Add `--project=<PROJECT_ID>` to every command that reads or changes resources, plus the `--region`, `--zone` or `--location` it needs; a missing location flag starts an interactive prompt. Unknown location: `gcloud compute regions list`, `gcloud compute zones list`, `gcloud run regions list`, or `gcloud <group> locations list` where the group has one (`run` and `storage` do not), each with `--project`; otherwise the leaf help lists the accepted values. The active configuration's project counts only after I confirmed it for the task. Done when no command relies on an unconfirmed default.
3. **Classify.** Read-only commands (`help`, `topic`, `list`, `describe`, `config list`, `operations describe`, `logging read`) run without asking, in a project I named or confirmed for the task. Every other command (create, update, deploy, add, set, enable, delete) goes through steps 4 and 5.
4. **Dry run.** If help lists `--dry-run` or `--validate-only`, run the command with it first. Most create, deploy and delete commands have neither: then `describe` the target and show its current state in step 5, and for `gcloud run deploy` propose `--no-traffic`. Done when the dry run passed, or the target's current state is in hand.
5. **Approval.** Tell me the command, the project, what it changes, what it costs and whether it can be undone, and wait for my explicit OK. The denylist below always waits for it, even inside an approved plan.

## Execution rules

- One gcloud command per call: no chaining, pipes, `$(...)` or redirection, so each command stays reviewable.
- Add `--quiet` so a prompt never hangs the shell. `--quiet` accepts the default answer, including "continue?" on a delete, so the approval in step 5 is the only safety gate.
- Long operations: use `--async` where help lists it, then poll with the group's own operations command where it has one (e.g. `gcloud compute operations describe <id>`; check with `gcloud help`).
- Flags and syntax come from `gcloud help` only; use the web for concepts and pricing.

## Keeping output small

- Every command that can return many items (`list`, `logging read`) gets `--limit`, `--filter` or `--format`; `logging read` also gets `--freshness` (e.g. `--freshness=1h`).
- Unknown field names: run `gcloud <group> <resource> list --limit=1 --format=json --project=<id>`, then request only the keys you need, e.g. `--format="json(name,status)"`.
- Filter server-side: `--filter="status:RUNNING"`, with no extra quotes on the right of `:`. The syntax references `gcloud topic filters`, `gcloud topic formats` and `gcloud topic projections` print 12-32k characters each: read one only when an expression fails, in a subagent that returns just the rule you need.

## Denylist: always my explicit OK first

- IAM policies, roles and bindings; service accounts and their keys.
- Enabling an API (`gcloud services enable`): it can provision resources and start billing.
- Installing or updating gcloud or its components.
- `gcloud ... delete`, `gcloud billing ...`, `gcloud organizations ...`, `gcloud kms ...`, `gcloud infra-manager deployments apply`.
