---
name: plan-feature
description: End-to-end feature planning workflow that sequences discovery, docs-aware grilling, optional prototyping, PRD creation, implementation spec, issue drafts, and optional Jira/Confluence artifacts. Use when the user wants to plan a feature, turn an idea into implementable work, use low-effort planning, or convert resolved decisions into planning artifacts.
---

# Plan Feature

Plan a feature by orchestrating existing skills. Do not duplicate their full workflows; load and use them at the right stage.

## Local-only rule

All outputs are local by default. Do NOT create, update, push, or publish anything to GitHub, GitLab, issue trackers, or other remote services unless the user explicitly asks for that specific remote action.

## Recovery breadcrumbs

For non-trivial planning sessions, load `agent-breadcrumbs` at the start and maintain `.agents/state/breadcrumbs/<agent-id>.md` through grilling, artifact creation, subagent orchestration, reviews, and verification checkpoints. Update it before long subagent calls and after each completed artifact so `connection-recovery` can reconstruct the plan if the connection drops.

## Default skill order

1. `grill-with-docs` - validate the feature against repo language, existing code, `CONTEXT.md`, and ADRs. Use this first for most feature planning because feature plans should fit the existing domain model.
2. `prototype` - optional. Use only when a core question is hard to settle in prose, such as state shape, workflow behavior, or UI direction.
3. `to-prd` - after grilling, spawn a subagent with `cavecrew` loaded to turn the resolved product intent into a local PRD; after that subagent finishes, spawn a separate subagent to review the PRD.
4. `to-spec` - after the PRD review is resolved, spawn a subagent with `cavecrew` loaded to turn the PRD into a concrete local implementation spec; after that subagent finishes, spawn a separate subagent to review the spec.
5. `to-issues` - after the spec review is resolved, spawn a subagent with `cavecrew` loaded to turn the spec into local, dependency-ordered issue drafts for agent workflows; after that subagent finishes, spawn a separate subagent to review the issue drafts.
6. `to-jira` - optional. Run only when the user requests Jira/Confluence or human-facing delivery docs; after that subagent finishes, spawn a separate subagent to review the Jira/Confluence docs.

## When to change the order

- If the user wants low-effort planning or answer choices, use the question tool when available and keep downstream artifact creation hands-off unless blocked.
- If the user already has resolved decisions, skip grilling and start with the first missing artifact.
- If the user already has an approved PRD, start at `to-spec`.
- If the user already has an approved spec, run `to-issues`; also run `to-jira` only when the user requests Jira/Confluence or human-facing delivery docs.
- If the user asks only to stress-test an idea, use `grill-with-docs` and stop.
- If the repo has no useful docs and the user only wants a conversational grilling session, use `grill-me` instead of docs-aware grilling.
- If the feature is primarily an architecture cleanup or deep refactor, use `improve-codebase-architecture` before `to-spec`.

## Process

### 1. Establish starting point

Identify what the user already has: idea, plan, PRD, spec, issue list, prototype, or code. If this can be answered by exploring the repo or reading referenced files, do that instead of asking.

If you must ask, ask one question at a time. If the question/choice tool is available and the question has bounded answers, use it with the recommended answer first.

### 2. Grill the plan

Load `grill-with-docs` by default. Interview the user relentlessly about unresolved product, domain, and implementation-shaping decisions. Walk down each branch of the design tree, resolving dependencies between decisions one by one.

For each question, provide your recommended answer. If a question can be answered by exploring the codebase, explore the codebase instead.

While grilling, use available MCP tools or skills when they can improve decision reasoning. Examples: use framework/domain docs tools for API or best-practice questions, project-specific skills for known technologies, and codebase tools for current implementation facts. Do not rely only on memory when a relevant tool can ground the recommendation.

### 3. Prototype only if needed

Load `prototype` only when there is a specific question that needs runnable feedback. Use it for throwaway validation, not as production implementation.

Capture the prototype answer before moving on. The answer belongs in the PRD/spec; the prototype code should stay disposable.

### 4. Create local docs

After grilling and any prototype checkpoint, use delegated artifact creation by default. If the user requested hands-off or low-effort planning, do not pause for extra approval between local artifacts unless a blocker appears. Load `cavecrew` before spawning creator and review subagents when available, and tell each creator subagent to use cavecrew for its own nested codebase/file investigation and review helpers where useful.

Run the artifact pipeline as create-then-review gates:

1. Spawn a `to-prd` creator subagent. After it finishes, spawn a separate PRD review subagent. Resolve review findings before continuing.
2. Spawn a `to-spec` creator subagent. After it finishes, spawn a separate spec review subagent. Resolve review findings before continuing.
3. Spawn a `to-issues` creator subagent from the reviewed spec. If Jira/Confluence docs were requested, also spawn a `to-jira` creator subagent.
4. After each creator subagent finishes, spawn a separate review subagent for its output. `to-issues` review and `to-jira` review can run in parallel when their outputs are independent.

Run `to-issues` and optional `to-jira` from the same reviewed spec. If there is no dependency between them, they can run in parallel. If the Jira/Confluence docs should reference local issue drafts, run `to-issues` first, complete its review, then pass the issue draft folder to the `to-jira` creator subagent.

Follow each skill's local storage rules exactly.

Expected default artifact chain:

- PRD: `docs/prds/YYYY-MM-DD-kebab-title.md`
- Spec: `docs/specs/YYYY-MM-DD-kebab-title.md`
- Issues: `docs/issues/<source-doc-name>/01-kebab-title.md`
- Optional Jira/Confluence: `docs/jira/<source-spec-name>/`

### 5. Stop at useful checkpoints

After PRD and spec checkpoints, summarize what exists locally. In interactive or low-effort mode, ask whether to continue at major checkpoints; low-effort changes answer mechanics, not approval gates. In explicit hands-off mode, keep going through local PRD, spec, issue drafts, and requested Jira/Confluence docs unless blocked. Do not publish remotely unless explicitly asked.

After all planning artifacts are complete, prepare concise follow-up prompts the user can choose from:

- More planning with `grill-with-docs` to stress-test unresolved domain, product, or architecture questions.
- Another planning pass with `plan-feature` for a related feature or follow-up slice.
- Implementation with `implement-feature-plan` using the completed local spec and issue drafts.

## Completion criteria

The feature is fully planned when the repo contains a local PRD, a local implementation spec, and local issue drafts ordered by dependency for agents, with no unresolved blocking questions. Jira/Confluence docs are required only when requested.
