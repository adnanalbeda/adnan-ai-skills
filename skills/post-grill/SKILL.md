---
name: post-grill
description: Run the same strict local planning artifact pipeline as plan-feature after grilling is already complete, without asking more grilling questions. Use when a feature idea, PRD, spec, or resolved decision set is ready for hands-off conversion into local PRD, spec, issue drafts, and Jira/Confluence docs.
---

# Post Grill

Turn already-resolved feature intent into local planning artifacts. This duplicates the downstream pipeline from `plan-feature`, but deliberately omits another grilling round.

Use this only after the product/domain/design questions are already resolved enough to plan. If required source material is missing or contradictory, stop with the smallest blocker question instead of starting a grilling process.

## Local-only rule

All outputs are local by default. Do NOT create, update, push, or publish anything to GitHub, GitLab, issue trackers, or other remote services unless the user explicitly asks for that specific remote action.

## Recovery breadcrumbs

Load `agent-breadcrumbs` at the start and maintain `.agents/state/breadcrumbs/<agent-id>.md` throughout strict hands-off artifact creation, subagent orchestration, reviews, and final handoff prompts. Update it before long subagent calls and after each completed artifact so `connection-recovery` can reconstruct the pipeline if the connection drops.

## Default skill order

1. `prototype` - optional. Use only when a core question is hard to settle from existing resolved material, such as state shape, workflow behavior, or UI direction.
2. `to-prd` - spawn a subagent with `cavecrew` loaded to turn resolved intent into a local PRD; after that subagent finishes, spawn a separate subagent to review the PRD.
3. `to-spec` - after the PRD review is resolved, spawn a subagent with `cavecrew` loaded to turn the PRD into a concrete local implementation spec; after that subagent finishes, spawn a separate subagent to review the spec.
4. `to-issues` - after the spec review is resolved, spawn a subagent with `cavecrew` loaded to turn the spec into local, dependency-ordered issue drafts for agent workflows; after that subagent finishes, spawn a separate subagent to review the issue drafts.
5. `to-jira` - after the spec review is resolved, spawn a subagent with `cavecrew` loaded to create human-facing Jira Epic, Story, Task files and Confluence documentation; after that subagent finishes, spawn a separate subagent to review the Jira/Confluence docs.

## When to change the order

- If the user already has an approved PRD, start at `to-spec` and run the remaining pipeline hands-off.
- If the user already has an approved spec, run both `to-issues` and `to-jira` hands-off unless the user explicitly asks to skip one.
- If the feature is primarily an architecture cleanup or deep refactor, use `improve-codebase-architecture` before `to-spec` only when existing resolved material does not already contain a concrete architecture direction.
- If required decisions are unresolved, ask the smallest blocker question and stop. Do not load `grill-me` or `grill-with-docs` from this skill.

## Process

### 1. Establish starting point

Identify what the user already has: resolved idea, plan, PRD, spec, issue list, prototype, or code. If this can be answered by exploring the repo or reading referenced files, do that instead of asking.

Do not run a grilling phase. Treat existing conversation, referenced docs, and repo artifacts as the source of truth. Ask only when a blocker prevents the next artifact from being created correctly.

### 2. Prototype only if needed

Load `prototype` only when there is a specific unresolved question that needs runnable feedback and cannot be answered from the resolved source material. Use it for throwaway validation, not production implementation.

Capture the prototype answer before moving on. The answer belongs in the PRD/spec; the prototype code should stay disposable.

### 3. Run strict hands-off planning pipeline

Do not pause for extra approval between planning artifacts. Run the downstream flow strictly until all local planning artifacts are complete.

Only stop before completion when:

- A product, domain, or architecture blocker remains unresolved after repo/artifact exploration.
- A downstream skill cannot proceed because required source material is missing or contradictory.
- A remote action would be required. Local files are allowed; remote publishing is not allowed without explicit user instruction.
- The user explicitly asks to pause or stop.

Use delegated artifact creation by default. Load `cavecrew` before spawning creator and review subagents when available, and tell each creator subagent to use cavecrew for its own nested codebase/file investigation and review helpers where useful.

Run the artifact pipeline as create-then-review gates:

1. Spawn a `to-prd` creator subagent. After it finishes, spawn a separate PRD review subagent. Resolve review findings before continuing.
2. Spawn a `to-spec` creator subagent. After it finishes, spawn a separate spec review subagent. Resolve review findings before continuing.
3. Spawn `to-issues` and `to-jira` creator subagents from the reviewed spec. These two creator subagents can run in parallel when Jira/Confluence docs do not need to reference local issue draft paths.
4. After each creator subagent finishes, spawn a separate review subagent for its output. `to-issues` review and `to-jira` review can also run in parallel when their outputs are independent.

Run `to-issues` and `to-jira` from the same reviewed spec. If there is no dependency between them, they can run in parallel. If the Jira/Confluence docs should reference local issue drafts, run `to-issues` first, complete its review, then pass the issue draft folder to the `to-jira` creator subagent.

Follow each skill's local storage rules exactly.

Expected default artifact chain:

- PRD: `docs/prds/YYYY-MM-DD-kebab-title.md`
- Spec: `docs/specs/YYYY-MM-DD-kebab-title.md`
- Issues: `docs/issues/<source-doc-name>/01-kebab-title.md`
- Jira/Confluence: `docs/jira/<source-spec-name>/`

### 4. Finish and offer next prompts

After PRD and spec checkpoints, summarize internally and keep going. Do not ask whether to continue. After the spec is reviewed, create both agent issue drafts and human-facing Jira/Confluence docs before the final summary. Do not publish remotely unless explicitly asked.

After all planning artifacts are complete, prepare concise follow-up prompts the user can choose from:

- Implementation with `implement-feature-plan` using the completed local spec and issue drafts.
- More planning with `post-grill` for a related already-resolved feature or follow-up slice.
- A separate planning pass with `plan-feature` only if new unresolved decisions need pressure-testing.

## Completion criteria

The feature is fully planned when the repo contains a local PRD, a local implementation spec, local issue drafts ordered by dependency for agents, and local Jira/Confluence docs for humans, with no unresolved blocking questions.
