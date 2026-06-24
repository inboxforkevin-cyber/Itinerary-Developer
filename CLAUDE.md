# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## WAT Framework Architecture

This repo uses a three-layer architecture that separates AI reasoning from deterministic execution:

- **Workflows** (`workflows/`) — Markdown SOPs defining objectives, required inputs, which tools to use, expected outputs, and edge case handling. These are your instructions.
- **Agent (you)** — Read the relevant workflow, run tools in sequence, handle failures, ask clarifying questions. Orchestrate; don't try to do everything directly.
- **Tools** (`tools/`) — Python scripts that do the actual work: API calls, data transforms, file operations, DB queries. Credentials live in `.env`.

**Why this separation matters:** Chaining 5 AI steps at 90% accuracy = 59% end-to-end success. Offloading execution to deterministic scripts keeps you focused on orchestration where you're most reliable.

## How to Operate

**Always check `tools/` before building anything new.** Only create scripts when nothing exists for the task.

**When something fails:**
1. Read the full error trace
2. Fix the script and retest — if it uses paid API calls, check before re-running
3. Update the workflow with what you learned (rate limits, timing quirks, better endpoints)

**Workflow discipline:** Don't create or overwrite workflow files without being asked. Workflows are durable instructions that get refined over time, not disposable session notes.

## File Conventions

| Location | Purpose |
|---|---|
| `tools/` | Python scripts for deterministic execution |
| `workflows/` | Markdown SOPs |
| `.tmp/` | Temporary intermediates — fully disposable, regenerated as needed |
| `.env` | All credentials and API keys |
| `credentials.json`, `token.json` | Google OAuth (gitignored) |

**Deliverables go to cloud services** (Google Sheets, Slides, etc.) where the user can access them directly. Local files are processing intermediates only.
