---
name: to-issues
description: Break a plan, spec, or PRD into local independently-grabbable issue drafts using tracer-bullet vertical slices. Use when user wants to convert a plan into issues, create implementation tickets, or break down work into issues.
---

# To Issues

Break a plan into independently-grabbable issues using vertical slices (tracer bullets).

## Local-only rule

All output is local by default. Do NOT create, update, push, or publish anything to GitHub, GitLab, issue trackers, or other remote services unless the user explicitly asks for that specific remote action.

The issue tracker and triage label vocabulary are only needed if the user explicitly asks you to publish remotely — run `/setup-matt-pocock-skills` if not.

## Recovery breadcrumbs

For non-trivial issue breakdowns, load `agent-breadcrumbs` and update `.agent/state/breadcrumbs/<agent-id>.md` after context gathering, slice drafting, user approval or assumed approval in a strict pipeline, and local issue-file creation so `connection-recovery` can reconstruct progress after interruption.

## Local file storage

Store issue drafts in the current repo, not in global agent config. If the repo already has an issue-draft or planning-doc convention, follow it.

If the issues are derived from a PRD or spec, group them under `docs/issues/<source-doc-name>/`, where `<source-doc-name>` is the source document filename without `.md`. Example: issues for `docs/prds/2026-05-19-checkout-flow.md` go under `docs/issues/2026-05-19-checkout-flow/`.

If there is no source PRD or spec, create files lazily under `docs/issues/YYYY-MM-DD-kebab-title/`.

Create one file per issue draft using dependency order and lowercase ASCII kebab-case: `01-kebab-title.md`, `02-kebab-title.md`, etc. If useful, create `README.md` in the same folder as an index with source context, dependency order, and links to each draft.

Until the user explicitly asks to publish remotely, use local filenames in `Blocked by` instead of remote issue identifiers.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes an issue reference (issue number, URL, or path) as an argument, fetch it from the issue tracker and read its full body and comments.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Issue titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

### 3. Draft vertical slices

Break the plan into **tracer bullet** issues. Each issue is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

Slices may be 'HITL' or 'AFK'. HITL slices require human interaction, such as an architectural decision or a design review. AFK slices can be implemented and merged without human interaction. Prefer AFK over HITL where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
</vertical-slice-rules>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?

Iterate until the user approves the breakdown.

### 5. Save local issue drafts

For each approved slice, save a local issue draft using the storage rules above or present it as a local draft. Use the issue body template below. These issues are considered ready for AFK agents, but do not publish them remotely unless the user explicitly asks.

Order issue drafts by dependency order (blockers first). If publishing is explicitly requested, publish in that order so you can reference real issue identifiers in the "Blocked by" field.

<issue-template>
## Parent

A reference to the parent issue on the issue tracker (if the source was an existing issue, otherwise omit this section).

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- A reference to the blocking ticket (if any)

Or "None - can start immediately" if no blockers.

</issue-template>

Do NOT close or modify any parent issue.
