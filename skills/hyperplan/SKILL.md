---
name: hyperplan
description: Hostile planning skill that runs a 5-lens adversarial critique using available team tools, task subagents, or sequential simulation, then distills defensible insights into a concrete planning handoff. Use when the user says hyperplan, hostile plan, adversarial planning, cross-critique, red-team this plan, or when a risky/vague plan needs maximum pressure before implementation.
---

# Hyperplan

Run adversarial planning before implementation. Use the strongest orchestration available, but do not assume `team_*` tools exist.

## Core Rule

Hyperplan is not consensus. Five hostile lenses attack the request and each other's assumptions. The main agent distills only defensible insights and then hands off to the appropriate planning or implementation skill.

## Tool Ladder

Use the strongest available orchestration, in this order:

1. `team_*` tools, if present and already enabled by the harness.
2. Parallel `task` subagents or category agents, if present.
3. Main-thread sequential role simulation, if no subagent/team tooling is available.

Do not stop only because team mode is unavailable. Degrade explicitly and continue.

## Roles

- **Pragmatist Skeptic**: attacks over-engineering, scope creep, and premature abstraction. Asks what can be deleted.
- **Integration Tester**: attacks missing edge cases, state transitions, failure modes, and blast radius.
- **Researcher**: attacks unsupported claims. Requires file, command, docs, or explicit "no evidence found".
- **Architect Strategist**: attacks coupling, boundary leaks, brittle interfaces, and technical debt.
- **Creative Challenger**: attacks first-thought framing and proposes concrete alternatives or reframes.

## Workflow

1. Restate the planning target in one sentence.
2. Gather baseline evidence first: repo docs, code references, existing plans, tests, constraints, and user request.
3. Run Round 1 independent analysis for all roles. Each role returns 3-7 numbered findings, each no more than 3 sentences.
4. Run Round 2 cross-attack: each role attacks the strongest claims from other roles, focusing on evidence gaps and downstream risk.
5. Distill surviving insights. Drop weak, duplicate, speculative, or scope-expanding claims.
6. Produce the planning handoff: objective, constraints, rejected paths, selected path, risks, verification strategy, and next skill to load.
7. Continue into `plan-feature`, `oh-my-ai-skill`, `ufw`, `diagnose`, or `implement-feature-plan` based on the handoff and user intent.

## Role Prompt Shape

Use this compact prompt for each role:

```md
Role: <role>
Target: <one-sentence plan/request>
Evidence available: <paths/docs/tests/constraints>

Task: Attack this plan from your role. Produce 3-7 numbered findings. Each finding must be specific, <=3 sentences, and include evidence, risk, or a concrete alternative. Do not write the final plan.
```

## Distillation Contract

```md
## Hyperplan Result

Target: <one sentence>
Mode: <team | parallel task | sequential fallback>

Surviving insights:
- <insight> - <evidence/risk>

Rejected attacks:
- <attack> - <why rejected>

Recommended path: <chosen approach>
Why: <short rationale>
Risks:
- <risk> - <mitigation>
Verification:
- <check/evidence>
Next skill: <plan-feature | oh-my-ai-skill | ufw | diagnose | implement-feature-plan>
```

## Quality Bar

- Evidence beats confidence.
- Simplicity wins unless evidence proves complexity pays now.
- Alternatives must be concrete, not novelty for novelty's sake.
- Do not implement during Hyperplan unless user only asked for a review and the next safe action is already approved by another active workflow.
- Escalate to the user only when no safe local assumption exists and the unresolved decision changes behavior, data shape, external contract, security, cost, or irreversible scope.
