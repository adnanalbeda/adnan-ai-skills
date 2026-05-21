---
name: agent-breadcrumbs
description: Maintain per-agent recovery breadcrumbs during long planning, implementation, review, or documentation tasks so interrupted work can be reconstructed after connection loss. Use before and during long-running workflows, subagent orchestration, multi-file edits, or strict planning pipelines.
---

# Agent Breadcrumbs

Create and maintain a small local recovery note while doing work that may span multiple turns, tools, subagents, files, or verification steps. This skill prepares context for `connection-recovery` if connection drops or an agent stops mid-task.

## Storage

Store breadcrumbs in the current repo/workspace at:

```md
.agents/state/breadcrumbs/<agent-id>.md
```

Use one file per concurrent agent/task. Do not share one breadcrumb file between parallel agents.

Choose `<agent-id>` in this order:

1. Runtime/tool agent id if one is available.
2. Fallback: timestamp plus short task slug, for example `2026-05-19-1832-lazy-plan-feature.md`.

Create parent directories lazily when first writing the breadcrumb.

## Recovery Pointer In Context

Whenever a breadcrumb is active, keep this compact pointer available in the session context:

```text
Recovery breadcrumb: .agents/state/breadcrumbs/<agent-id>.md
```

Emit the pointer after the first breadcrumb write. Repeat it at recovery-sensitive moments: phase changes, before long commands or subagent calls, before stopping for a user decision, and in handoff or recovery summaries.

Do not repeat it in every message. Keep the line exact and compact so `connection-recovery` can parse it after a drop. The pointer is only a lookup hint; recovery must still verify the breadcrumb before continuing.

## Current State Snapshots

Breadcrumbs keep a latest-first history of current state snapshots. On every update, prepend a new `## Current State - <local timestamp>` section above older current state sections. Do not replace or delete older snapshots unless the user asks.

Keep older snapshots concise enough for recovery. If the file becomes noisy during very long work, preserve the latest few meaningful snapshots and summarize older ones under `## Earlier State Summary` instead of deleting context silently.

## Required Fields

Every current state snapshot must include:

```md
# Agent Breadcrumb

Agent: <agent-id>
Goal: <user goal in one sentence>

## Current State - <local timestamp>

Intent: <planning | implementation | review | commit | explanation | other>
Current phase: <where the work is now>
Last completed step: <latest completed meaningful step>
Active files/artifacts:
- <path> — <why it matters>
Next safe action: <one concrete next action>
Blockers: <none | unresolved question or dependency>
Verification status: <not run | command/result | not applicable>
Last updated: <local timestamp>
```

Add optional fields only when useful:

- Decisions made
- Rejected options that affect later work
- Subagents running or completed
- Commands already run
- Tests/builds still needed
- User explicitly requested pauses or constraints

## When To Update

Update the breadcrumb:

- At task start for long-running work.
- After each completed meaningful step.
- Before risky phase changes, such as planning to implementation or editing to verification.
- Before long tool calls, test/build commands, or subagent orchestration.
- After subagents finish and their outputs change next steps.
- Before stopping for a blocker or user decision.

Do not update after every tiny read/search unless it changes the recovery state.

Each update prepends a complete new current state snapshot. Older snapshots remain below it as recovery history.

## Writing Rules

- Always use the relevant caveman skill/style when writing or updating breadcrumbs. Breadcrumbs are for agents, so prefer compressed agent-readable notes over human-facing prose.
- Keep the breadcrumb concise. It is a recovery index, not a transcript.
- Prefer paths, artifact names, and concrete next actions over prose.
- Never store secrets, credentials, tokens, or private data that is not already safe in project files.
- If the repo has existing `.gitignore` rules for `.agents/`, respect them. Do not modify `.gitignore` unless the user asks.
- Do not commit breadcrumb files unless the user explicitly asks.

## Completion

When the task is complete, update the breadcrumb with final status:

```md
## Current State - <local timestamp>

Current phase: complete
Last completed step: <final outcome>
Next safe action: none
Blockers: none
Verification status: <final verification>
```

Leave the file in place for future recovery unless the user asks to delete it.
