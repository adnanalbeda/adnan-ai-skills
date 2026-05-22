---
name: implement-feature-plan
description: Execute an existing feature plan from PRDs, implementation specs, issue drafts, or planning artifacts. Use when the user asks to build from prepared docs, implement issue drafts, continue a planned feature, or work from docs/prds, docs/specs, docs/issues, or docs/jira.
---

# Implement Feature Plan

Implement artifacts produced by `plan-feature` or `oh-my-ai-skill`. Agent-focused issue drafts drive execution; the spec is the source of truth; PRD and Jira/Confluence docs provide context.

## Subagent delegation

Use subagents by default to preserve main-thread context during feature-plan execution. Load `cavecrew` before delegating when available, and prefer cavecrew agents for compressed outputs.

- Spawn 2-3 `cavecrew-investigator` agents in parallel for broad codebase discovery, using different search angles such as prior art, tests, data flow, API boundaries, and config.
- Use `cavecrew-builder` only for obvious surgical edits scoped to 1-2 known files after investigation identifies exact targets.
- Use `cavecrew-reviewer` for bug-focused review of non-trivial diffs before verification, especially when multiple files or acceptance criteria are involved.
- Keep orchestration, product decisions, cross-cutting architecture choices, and 3+ file implementations in the main thread unless a normal subagent is a better fit.
- Aggregate subagent findings into the implementation queue; do not expose raw terse cavecrew output to the user unless it is already clear enough.

## Local-only rule

All work stays local by default. Do NOT push branches, create PRs, update Jira, publish Confluence, or mutate other remote services unless the user explicitly asks for that specific remote action.

Do not commit unless the user explicitly asks. If committing is requested, follow the repository's normal git safety rules.

## Recovery breadcrumbs

For non-trivial implementation work, load `agent-breadcrumbs` at the start and maintain `.agents/state/breadcrumbs/<agent-id>.md` through issue selection, edits, subagent orchestration, reviews, tests, and acceptance-criteria verification. Update it before long commands/subagent calls and after each completed slice so `connection-recovery` can reconstruct where implementation stopped.

## Inputs

Expected local artifact chain:

- PRD: `docs/prds/YYYY-MM-DD-kebab-title.md`
- Spec: `docs/specs/YYYY-MM-DD-kebab-title.md`
- Agent issues: `docs/issues/<source-doc-name>/01-kebab-title.md`
- Human docs: `docs/jira/<source-spec-name>/`

If the user gives a path, start there. If multiple matching plans exist, ask which one to implement using the question/choice tool when available. If running under `oh-my-ai-skill`, first use current run context, artifact links, timestamps, and dependency order to choose; stop only if no safe local choice exists.

If no spec or issue drafts exist, load `plan-feature` or the missing planning skill instead of guessing the implementation plan. If running under `oh-my-ai-skill`, return to its artifact pipeline for missing spec or issue drafts instead of starting a separate planning workflow.

## Authority order

When artifacts conflict, prefer them in this order:

1. Current user instruction
2. Local spec
3. Agent issue drafts
4. PRD
5. Jira/Confluence docs
6. Existing code conventions

Jira and Confluence docs are for people to read. Do not treat them as more authoritative than the spec or agent issue drafts.

## Process

### 1. Load plan context

Read the spec, issue draft folder, PRD if linked, and Jira/Confluence docs if present. Explore the codebase to verify current state and identify prior art before editing.

Build an implementation queue from unblocked AFK and resolved HITL issue drafts in dependency order. Skip blocked drafts unless the blocker is already complete in the repo. Skip unresolved HITL drafts until their required human decision or review is resolved and recorded.

### 2. Select work slice

Implement one vertical slice at a time. Prefer the next unblocked AFK or resolved HITL issue draft. If the user asks for a specific issue, implement that issue and its required blockers first, unless a HITL decision remains unresolved.

For broad investigation, use `cavecrew-investigator` or `Explore` before editing. Use `cavecrew-builder` only for obvious 1-2 file surgical edits after files are known.

### 3. Implement minimal change

Use the project's existing patterns, commands, and style. Load relevant domain skills when applicable, such as Angular, .NET, Quartz, LiteBus, or Storybook skills.

Use `tdd` when the issue/spec calls for tests, when behavior is complex, or when regression risk is meaningful. Work red-green-refactor by vertical slice, not horizontal batches.

Keep changes minimal. Do not add backwards compatibility unless the spec, persisted data, shipped behavior, external consumers, or user explicitly require it.

### 4. Review before testing

Review the diff before running tests. Check for obvious syntax mistakes, missed acceptance criteria, accidental scope creep, unrelated edits, broken conventions, and tests that need to be added or adjusted.

Use `cavecrew-reviewer` for a terse bug-focused review when the diff is large enough to benefit from a second pass. Use main-thread self-review for small changes.

### 5. Verify acceptance criteria

For each completed issue draft:

- Check every acceptance criterion against actual behavior.
- Run the narrowest useful tests first, then broader test/build commands if the slice affects integration points.
- Fix failures, then re-run the relevant verification.
- Do a final review after tests pass for regressions, accidental edits, missing docs, and unchecked criteria.
- Update local issue checkboxes only after review and verification both pass.
- Note any skipped verification and why.

### 6. Continue or stop

After each slice, summarize completed files, verified criteria, and remaining issue drafts. Continue to the next unblocked slice unless the user asked for only one slice or a checkpoint is needed. If running under a strict hands-off parent workflow such as `oh-my-ai-skill`, do not stop for routine checkpoints; update the parent run notes and continue.

Stop and ask when implementation requires a product/architecture decision not answered by the spec or codebase. If running under `oh-my-ai-skill`, first use its option-study decision resolution policy; ask only after the resolver finds no safe local path.

## Completion criteria

The feature plan is implemented when all unblocked local issue drafts are complete, acceptance criteria are verified, tests/builds relevant to the touched areas pass, and unresolved blockers are documented locally.
