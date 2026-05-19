---
name: to-spec
description: Turn a PRD, plan, issue, or conversation context into a concrete local implementation spec. Use when user wants to create a spec, technical spec, implementation spec, or convert context into a spec.
---

# To Spec

Create an implementation spec that is precise enough for an agent to build from, but stable enough not to go stale immediately.

## Local-only rule

All output is local by default. Do NOT create, update, push, or publish anything to GitHub, GitLab, issue trackers, or other remote services unless the user explicitly asks for that specific remote action.

The issue tracker and triage label vocabulary are only needed if the user explicitly asks you to publish remotely - run `/setup-matt-pocock-skills` if not.

## Recovery breadcrumbs

For non-trivial spec creation, load `agent-breadcrumbs` and update `.agent/state/breadcrumbs/<agent-id>.md` after source-material gathering, codebase exploration, gap resolution, draft creation, and local save so `connection-recovery` can reconstruct progress after interruption.

## Local file storage

Store specs in the current repo, not in global agent config. If the repo already has a spec or planning-doc convention, follow it. Otherwise create files lazily under `docs/specs/` using `YYYY-MM-DD-kebab-title.md`.

Use lowercase ASCII kebab-case for filenames. Start the file with an H1 title, then include the spec template. If the spec came from a PRD, issue, or conversation reference, include that source near the top of `Current State` instead of encoding it in the filename.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes an issue reference, PRD reference, URL, or path as an argument, fetch it and read its full body and comments.

### 2. Explore the codebase

Explore the repo to understand the current implementation and relevant prior art. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

Look specifically for:

- Existing modules, seams, and public interfaces related to the work
- Similar features, tests, migrations, jobs, commands, events, API endpoints, or UI flows
- Constraints from persistence, external integrations, background processing, auth, permissions, observability, and deployment

### 3. Resolve blocking gaps

Do NOT run a broad interview. Ask only about decisions that materially change behavior or architecture and cannot be answered from the repo or source material. For each question, provide your recommended answer.

If the gap can be safely captured as an explicit assumption, include it in the spec instead of blocking.

### 4. Draft the spec

Write the spec using the template below. Prefer durable names and interfaces over fragile file paths. Avoid code snippets unless a prototype produced a snippet that encodes a decision more precisely than prose can, such as a state machine, reducer, schema, or type shape.

### 5. Review and save locally

Present the spec summary and any open questions to the user. Once approved, save it locally using the storage rules above or present it as a local draft. If the user explicitly asks you to publish it remotely, apply the `ready-for-agent` triage label only if there are no unresolved blocking questions.

<spec-template>

## Goal

The concrete outcome this implementation should deliver.

## Current State

Relevant existing behavior, domain language, constraints, and prior art.

## Proposed Behavior

The intended behavior from the system's point of view, including important edge cases and failure modes.

## Design

The modules, interfaces, contracts, workflows, schemas, events, jobs, or UI states that will be created or changed. Describe responsibilities and boundaries, not step-by-step coding instructions.

## Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Testing Plan

What should be tested, at what level, and which existing tests are useful prior art. Focus on external behavior, not implementation details.

## Rollout and Migration

Any data migrations, deployment ordering, feature flags, backfills, compatibility constraints, or operational checks. Write "None" if not applicable.

## Out of Scope

Work that is explicitly not part of this spec.

## Open Questions

Questions that must be answered before implementation. Write "None" if there are no blockers.

</spec-template>
