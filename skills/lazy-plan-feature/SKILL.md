---
name: lazy-plan-feature
description: Plan a feature end-to-end with lazy, choice-driven grilling, then run the full local planning artifact pipeline hands-off. Uses lazy-docs and lazy-grill instead of normal grilling skills.
---

# Lazy Plan Feature

Plan a feature by orchestrating existing skills. Do not duplicate their full workflows; load and use them at the right stage.

This duplicates `plan-feature`, but uses `lazy-docs` and `lazy-grill` for grilling. After grilling is complete, the downstream planning flow is strict and hands-off: continue through local PRD, spec, issue drafts, and Jira/Confluence docs without asking for extra confirmation unless there is a blocker, ambiguity that cannot be resolved from the repo/artifacts, or an explicit remote action request.

## Local-only rule

All outputs are local by default. Do NOT create, update, push, or publish anything to GitHub, GitLab, issue trackers, or other remote services unless the user explicitly asks for that specific remote action.

## Recovery breadcrumbs

Load `agent-breadcrumbs` at the start and maintain `.agent/state/breadcrumbs/<agent-id>.md` throughout lazy grilling, strict hands-off artifact creation, subagent orchestration, reviews, and final handoff prompts. Update it before long subagent calls and after each completed artifact so `connection-recovery` can reconstruct the pipeline if the connection drops.

## Default skill order

1. `lazy-docs` - validate the feature against repo language, existing code, `CONTEXT.md`, and ADRs using lazy choice-driven grilling. Use this first for most feature planning because feature plans should fit the existing domain model.
2. `prototype` - optional. Use only when a core question is hard to settle in prose, such as state shape, workflow behavior, or UI direction.
3. `to-prd` - after grilling, spawn a subagent with `cavecrew` loaded to turn the resolved product intent into a local PRD; after that subagent finishes, spawn a separate subagent to review the PRD.
4. `to-spec` - after the PRD review is resolved, spawn a subagent with `cavecrew` loaded to turn the PRD into a concrete local implementation spec; after that subagent finishes, spawn a separate subagent to review the spec.
5. `to-issues` - after the spec review is resolved, spawn a subagent with `cavecrew` loaded to turn the spec into local, dependency-ordered issue drafts for agent workflows; after that subagent finishes, spawn a separate subagent to review the issue drafts.
6. `to-jira` - after the spec review is resolved, spawn a subagent with `cavecrew` loaded to create human-facing Jira Epic, Story, Task files and Confluence documentation; after that subagent finishes, spawn a separate subagent to review the Jira/Confluence docs.

## When to change the order

- If the user already has an approved PRD, start at `to-spec` and run the remaining pipeline hands-off.
- If the user already has an approved spec, run both `to-issues` and `to-jira` hands-off unless the user explicitly asks to skip one.
- If the user asks only to stress-test an idea, use `lazy-docs` and stop.
- If the repo has no useful docs and the user only wants a conversational lazy grilling session, use `lazy-grill` instead of `lazy-docs`.
- If the feature is primarily an architecture cleanup or deep refactor, use `improve-codebase-architecture` before `to-spec`.

## Process

### 1. Establish starting point

Identify what the user already has: idea, plan, PRD, spec, issue list, prototype, or code. If this can be answered by exploring the repo or reading referenced files, do that instead of asking.

If you must ask, use `lazy-grill` style: ask one question at a time, list candidate answers, show the recommended answer first, and use the question/choice tool when available.

### 2. Grill the plan

Load `lazy-docs` by default. Interview the user relentlessly about unresolved product, domain, and implementation-shaping decisions. Walk down each branch of the design tree, resolving dependencies between decisions one by one.

For each question, provide your recommended answer using `lazy-grill` active-recommendation flow. If a question can be answered by exploring the codebase, explore the codebase instead.

While grilling, use available MCP tools or skills when they can improve decision reasoning. Examples: use framework/domain docs tools for API or best-practice questions, project-specific skills for known technologies, and codebase tools for current implementation facts. Do not rely only on memory when a relevant tool can ground the recommendation.

### 3. Prototype only if needed

Load `prototype` only when there is a specific question that needs runnable feedback. Use it for throwaway validation, not as production implementation.

Capture the prototype answer before moving on. The answer belongs in the PRD/spec; the prototype code should stay disposable.

### 4. Run strict hands-off planning pipeline

After grilling and any needed prototype answer are complete, do not pause for extra approval between planning artifacts. Run the downstream flow strictly until all local planning artifacts are complete.

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

### 5. Finish and offer next prompts

After PRD and spec checkpoints, summarize internally and keep going. Do not ask whether to continue. After the spec is reviewed, create both agent issue drafts and human-facing Jira/Confluence docs before the final summary. Do not publish remotely unless explicitly asked.

After all planning artifacts are complete, prepare concise follow-up prompts the user can choose from:

- More planning with `lazy-docs` to stress-test unresolved domain, product, or architecture questions.
- Another planning pass with `lazy-plan-feature` for a related feature or follow-up slice.
- Implementation with `implement-feature-plan` using the completed local spec and issue drafts.

## Completion criteria

The feature is fully planned when the repo contains a local PRD, a local implementation spec, local issue drafts ordered by dependency for agents, and local Jira/Confluence docs for humans, with no unresolved blocking questions.
