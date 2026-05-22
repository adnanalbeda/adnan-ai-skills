---
name: ufw
description: Ultra Focused Work (UFW) workflow for IntentGate-routed, deep, uninterrupted agent execution with explicit objective, constraints, work queue, verification loop, subagent routing, and concise progress state. Use when the user says UFW, ultra work, ultrawork, ultra-focused work, deep work mode, or asks the agent to keep working through a bounded goal without routine interruptions.
---

# UFW

UFW means **Ultra Focused Work**: a bounded, high-intensity execution loop for one goal. It keeps work moving, minimizes chat, preserves state, verifies outcomes, and interrupts only for true blockers.

## When To Use

- User says `UFW`, `ultra work`, `ultrawork`, `ultra-focused work`, `deep work`, or `focus mode`.
- A clear bounded outcome exists and progress should continue without routine confirmations.
- Work benefits from a loop: plan next slice, execute, verify, update state, repeat.
- User wants fewer messages and more autonomous execution, but not unsafe actions.

Do not use UFW for vague brainstorming, open-ended product discovery, irreversible operations, external publishing, credential handling, or any task that first needs a human decision.

## Core Contract

- One goal at a time.
- Classify intent before work; do not blindly execute the literal words if likely intent differs.
- Keep a compact queue of next actions.
- Work smallest useful slice first.
- Verify each slice before marking it done.
- Report progress only at meaningful phase changes, blockers, or completion.
- Stop only for true blockers: unsafe action, missing credential/access, conflicting requirement, destructive step, external publish/deploy/spend, or repeated verification failure after distinct fixes.

## Execution Discipline

- Use the harness's best available routing: `task(category=...)`, `task(subagent_type=...)`, `team_*`, or plain local tools. Do not assume unavailable tools exist.
- Run independent subagents in parallel for exploration, docs, architecture, or review when it shortens the critical path.
- Keep main context lean: store state in breadcrumbs or a run note, then continue from artifact paths.
- Prefer evidence over confidence. Each completed slice needs a file, command, test, or reviewed artifact as proof.
- If idle or confused, ask one internal question: "what acceptance criterion remains unverified?" Then act on that.

## Start Protocol

At start, define:

- **Objective**: exact outcome.
- **Done state**: observable completion condition.
- **Intent route**: implement, diagnose, review, plan, or artifact generation.
- **Constraints**: user rules, safety limits, repo conventions, time/budget limits.
- **Queue**: 3-7 concrete slices, ordered by dependency.
- **Verification**: commands, tests, manual checks, or artifact review needed.

If objective or done state is ambiguous and guessing could change scope, ask one short clarifying question. If ambiguity is low-risk and reversible, choose the smallest safe assumption and record it.

## Work Loop

Repeat until done or blocked:

1. Pick next unblocked slice.
2. Gather only context needed for that slice; parallelize search/review when useful.
3. Make minimal correct change or produce the required artifact.
4. Verify with narrowest useful signal.
5. Fix failures with new evidence; do not repeat same attempt blindly.
6. Update queue and state.
7. If a slice reveals risk or vague requirements, run `council` or `hyperplan` before expanding scope.
8. Continue without asking if next step is safe and inside scope.

## Progress Updates

Use terse updates. Good moments:

- UFW start: objective, queue, first slice.
- Phase change: exploration to edit, edit to verification, verification to next slice.
- Material discovery: hidden constraint, risk, or changed path.
- Blocker: what blocked, what was tried, next safe options.
- Completion: outcome, files/artifacts, verification.

Avoid routine narration like reading files, running obvious searches, or minor edits.

## State Format

Keep compact state in breadcrumb, run note, or chat when useful:

```md
Objective: <goal>
Done when: <observable state>
Intent route: <implement | diagnose | review | plan | artifact>
Queue:
- [x] <done slice>
- [ ] <next slice>
- [ ] <later slice>
Current: <active slice>
Assumptions: <none | compact list>
Verification: <not run | command/result>
Blockers: <none | blocker>
```

## Autonomy Boundaries

- Allowed: local file edits, local docs/artifacts, local tests/builds, reversible assumptions, subagent research/review, non-destructive diagnostics.
- Ask first: commits, pushes, PRs, external tickets, deploys, paid services, secret access, destructive commands, irreversible migrations, public exposure, broad scope changes.
- If user already authorized one of these explicitly, keep within the exact authorization.

## Verification

- Prefer deterministic verification: tests, build, lint, typecheck, reproducible script, artifact self-review.
- If full verification is expensive, run the narrowest useful check first and state residual risk.
- Do not mark done if verification failed unless the user asked for best-effort only.
- When no runnable verification exists, perform a structured review against acceptance criteria and say verification is review-only.

## Relationship To Other Skills

- Use `agent-breadcrumbs` for long UFW runs.
- Use `council` for hard trade-offs or blockers inside UFW.
- Use `hyperplan` when the route or implementation plan needs adversarial critique before execution.
- Use `diagnose` when UFW goal is a hard bug or performance regression.
- Use `implement-feature-plan` when UFW goal is executing existing PRD/spec/issues.
- Use `oh-my-ai-skill` when user wants the full docs-aware planning-to-implementation pipeline, not just focused execution.

## Completion Report

End with:

- What changed or was produced.
- Verification result.
- Remaining risks or skipped checks.
- Natural next step, only if useful.
