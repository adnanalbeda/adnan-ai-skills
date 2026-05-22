---
name: oh-my-ai-skill
description: End-to-end local agent workflow for IntentGate-style triage, docs-aware grilling, hostile subagent council analysis, PRD/spec/issue artifact creation, internal review gates, context hygiene, and Ralph-loop implementation. Use when the user asks to run `oh-my-ai-skill`, `/oh-my-ai-skills`, requests uninterrupted planning-to-implementation, mentions Ralph Loop as a workflow, or asks subagents to plan, review, and implement generated issues without human stage gates.
---

# Oh My AI Skill

Orchestrate existing skills into an uninterrupted local workflow. Do not duplicate their full instructions; load and use the right skill at each stage.

## Core Rule

Do not interrupt the user for routine local planning transitions, local artifact approvals, compaction, or reversible assumptions. Continue by using repo evidence, subagents, conservative defaults, and local artifacts. Surface a blocker only after exhausting safe local and subagent options. Never suppress safety, security, irreversible-action, credential, external-publish, spend, deploy, or destructive-operation confirmations required by the active agent rules.

## Local-Only Boundary

All work is local by default. Do not push, publish, create PRs, mutate Jira/Confluence/GitHub, commit, deploy, spend money, expose secrets, or run destructive/irreversible actions unless the user explicitly requested that exact action.

## Operating Patterns

Use these patterns as workflow behavior. They describe how the agent should think, route, delegate, and verify work regardless of which runtime tools are available.

- **IntentGate**: classify true user intent before acting. Route to planning, implementation, diagnosis, review, or focused work instead of literal keyword matching.
- **Discipline agent loop**: maintain a queue, keep working until the done state is met, and stop only for true blockers.
- **Category routing**: choose subagents by work type and model/tool strengths when the harness supports it; otherwise emulate with the best available `task`/subagent tool.
- **Parallel evidence**: use independent agents for repo search, architecture, docs, and review when it saves context or exposes blind spots.
- **Hostile review**: attack plans before implementation. Prefer adversarial findings over consensus vibes.
- **Context hygiene**: keep state in breadcrumbs, run handoffs, issue checkboxes, and concise summaries instead of long chat transcripts.
- **Verification-first completion**: mark work complete only after acceptance criteria have evidence.

## Required Skills

- `agent-breadcrumbs` for long-running recovery state.
- `grill-with-docs` as source material for docs-aware grilling behavior; run it through this skill's AFK policy instead of its interactive wait loop.
- `cavecrew` when compressed subagent output helps preserve context.
- `council` or `hyperplan` only for hard trade-offs, hostile planning, and review gates.
- `ufw` for bounded uninterrupted execution slices inside the larger run.
- `to-prd`, `to-spec`, and `to-issues` for planning artifacts.
- `implement-feature-plan` for execution from reviewed spec and issue drafts.

Use domain skills and MCPs when the repo technology calls for them.

## IntentGate Pass

Before planning or editing, classify the user request and record the route in the breadcrumb or run note.

| Intent | Route |
| --- | --- |
| Clear bounded execution | Use `ufw` loop directly, with breadcrumbs and verification. |
| Vague feature or large change | Run AFK docs-aware grilling, then artifacts, then Ralph loop. |
| Existing PRD/spec/issues | Skip grilling unless contradictions appear; load `implement-feature-plan`. |
| Hard bug/perf regression | Load `diagnose`, then return to this workflow for artifacts if needed. |
| Plan smells risky | Load `hyperplan` or hostile `council` before artifacts. |
| Review requested | Run findings-first review; implement fixes only if user asked. |

If route is ambiguous but reversible, choose the smallest safe route and record the assumption. If route changes behavior, data shape, external contract, safety, or cost, stop for a blocker after council analysis.

## No-Interruption Policy

Interruption means requiring a human message to continue. Avoid it.

- Do not ask "continue?" after grilling, PRD creation, review, spec creation, issue generation, or each implemented issue.
- If a non-safety ambiguity appears, first try repo/docs/code evidence and council analysis. If it still is not resolved and the decision is reversible, choose the smallest safe assumption, record it in the relevant artifact, and continue.
- If an unresolved product, domain, or architecture decision would materially change behavior, data shape, external contracts, security, or implementation cost, treat it as a blocker instead of guessing.
- If a non-safety blocker appears, spawn subagents first to research, debate, and propose a safe path. For destructive actions, secrets/credentials, external publishing, spend, deploys, or required confirmations, stop for the user instead.
- If no safe local path exists, write the blocker and attempted paths into the run notes, then report it.

During downstream artifact creation, this skill supplies assumed approval to `to-prd`, `to-spec`, and `to-issues` approval checkpoints. Assumed approval applies only to local PRD/spec/issue artifact progression. It is valid only after the source decision set is resolved enough for planning and no blocking open questions remain. It never authorizes commits, remote publishing, external service mutation, deploys, spending, destructive actions, secret exposure, or unresolved HITL decisions. Record the assumed approval and its source in the artifact or run notes.

## Unresolved Decision Resolution

When repo/docs/code evidence does not resolve a non-safety decision, do not guess directly. Use option-study resolution:

1. Propose at least two concrete options, such as `option-a` and `option-b`.
2. Spawn one agent per option. Each option agent studies only its assigned option and returns consequences, evidence, risks, and downstream effects.
3. Collect the option summaries.
4. Spawn a resolver agent to choose the best decision from the summaries based on evidence, reversibility, risk, and fit with existing docs/code.
5. Record the resolved decision, evidence, rejected option, and remaining risks in the decision tree, run notes, PRD, or spec.

Stop for the user only when the resolver finds no safe local decision because the unresolved choice materially changes behavior, data shape, external contracts, security, cost, or irreversible scope.

## Subagent Council

Use a council for hard questions, contradictions, major trade-offs, and review gates. Use `hyperplan` when planning needs hostile cross-critique. Main agent owns synthesis; subagents provide evidence.

Default roles:

- **Explorer**: repo facts, docs, code paths, prior art.
- **Oracle**: architecture, domain boundaries, long-term risk.
- **Skeptic**: assumption attacks, failure modes, scope creep.
- **Builder**: implementation cost, simple path, test impact.
- **Creative Challenger**: lateral alternatives and reframes so the obvious path earns its win.

Council output should include recommendation, evidence, risks, example, and downstream effect. Run another council round only when findings conflict or expose a missing branch.

If subagent tooling is unavailable, run the same roles sequentially in the main thread and keep the same output shape. Record council summaries in breadcrumbs or the run handoff, not as long chat transcripts.

## AFK Docs-Aware Grilling

Use docs-aware grilling behavior autonomously. The goal is still to challenge the plan against repo language, code, docs, and concrete scenarios; the difference is that the agent resolves everything locally where safe before asking.

### Context Discovery

- Read existing domain docs before accepting the plan, bounded by `CONTEXT-MAP.md`, user paths, source artifacts, and targeted searches. Prefer relevant `CONTEXT.md`, ADRs, decision trees, current PRDs/specs/issues, and code over broad repo-wide reads.
- If `CONTEXT-MAP.md` exists, use it to choose the relevant bounded context. Otherwise treat root `CONTEXT.md` and targeted `docs/` searches as the default context.
- Create docs lazily. Create a decision-tree file when the first material question is recorded; update `CONTEXT.md` only after a glossary term is resolved. If no `docs/adr/` exists, create it only when an ADR passes the ADR test below.

### Autonomous Question Loop

For each product, domain, architecture, integration, data, UX, testing, and rollout decision that could shape implementation:

1. State the decision as a question in the decision tree.
2. Gather evidence from docs, code, tests, conventions, and source artifacts.
3. If evidence resolves it, record the recommended answer, final answer, evidence/source, decision effect, and example/demo without asking.
4. If evidence conflicts or the decision is high-impact, run council analysis and record recommendation, evidence, risks, example/demo, and downstream effect.
5. If still unresolved but reversible and low-risk, choose the smallest safe assumption, record it as the recommended and final answer, mark it as an assumption, and continue.
6. If still unresolved and it materially changes behavior, data shape, external contracts, security, implementation cost, or irreversible scope, record a blocker and stop only after council analysis finds no safe local path.

### Glossary And Domain Pressure

- Challenge terms against `CONTEXT.md`: if user language conflicts with existing glossary, prefer the glossary only when code/docs support it; otherwise record the conflict and resolve by evidence, council, or blocker policy.
- Sharpen vague or overloaded terms into one canonical term. Record only resolved glossary terms in `CONTEXT.md` using the local `CONTEXT-FORMAT.md` when present; do not use it as a scratchpad, spec, or implementation notes file.
- Discuss concrete scenarios internally. Invent edge cases that force boundary clarity, then resolve them from repo evidence or record assumptions/blockers.
- Cross-reference claimed behavior with code. If code contradicts the plan, record the contradiction, choose the code-backed behavior only when it matches the intended outcome, or escalate through council.

### Decision Tree Tracking

- Create or update one topic file under `docs/grill-answers-tree/` for each run. Use the local `DECISION-TREE-FORMAT.md` when present.
- If the topic has multiple aspects, list all aspects and mark each as `open`, `resolved`, `blocked`, or `deferred`.
- After every concrete answer, record the question, recommended answer, final answer, evidence/source, decision effect, example/demo, and whether it is an assumption.
- Keep the tree current as work progresses. Mark the overall session `resolved` only when no blocking open questions remain for local planning.

### ADR Test

Create an ADR only when all three are true:

1. The decision is hard to reverse.
2. The decision would be surprising without context.
3. The decision reflects a real trade-off between viable alternatives.

If any condition is missing, skip the ADR and keep the reasoning in the decision tree, PRD, or spec. When creating an ADR, use the local `ADR-FORMAT.md` when present.

## Workflow

1. Load `agent-breadcrumbs`, follow repo breadcrumb location/field/pointer rules, and load only relevant context selected by `CONTEXT-MAP.md`, user paths, source artifacts, ADRs, existing plans, and targeted searches.
2. Run IntentGate pass and choose the route.
3. Run AFK docs-aware grilling using the rules above; if a question can be answered from repo/code/docs, answer it by exploration instead of asking. If a question cannot be answered safely, use the no-interruption policy to decide whether to assume or stop for a true high-impact blocker.
4. Use `hyperplan` or council subagents for answer-option analysis when the decision is hard, high-impact, vague, or likely to overfit the first solution.
5. Record concrete decisions immediately in `docs/grill-answers-tree/`; update `CONTEXT.md` only for resolved glossary terms.
6. When grilling is resolved, run artifact pipeline without human approval gates.
7. Keep main context lean with subagents, breadcrumbs, artifact paths, and concise run notes. Do not require human-triggered compaction.
8. Start Ralph loop from reviewed issue drafts and keep going until all resolvable issues are complete.

## Artifact Pipeline

Use create-then-review gates. Reviews are internal quality gates, not human approval gates.

1. Spawn PRD creator with `to-prd`; spawn separate PRD reviewer; record findings and resolutions; resolve blocking findings.
2. Spawn spec creator with `to-spec`; spawn separate spec reviewer; record findings and resolutions; resolve blocking findings.
3. Spawn issue creator with `to-issues`; for multiple reviewed specs, assign one issue creator per spec.
4. Spawn issue reviewer; record findings and resolutions; resolve blocking findings before implementation. If subagents are unavailable, run creator then reviewer passes sequentially in the main thread. Re-review until no blocking findings remain or a named blocker is recorded.
5. Optional human-facing Jira/Confluence docs may be created as local files only when explicit user intent or the active planning workflow requires them; never publish or mutate remote systems by default.

Reviewer gates must check plan quality:

- File references are verified or marked as assumptions.
- Acceptance criteria are measurable.
- Failure modes and rollback/safety paths are named for high-impact changes.
- Scope can be implemented in vertical slices.
- No task requires hidden business-logic guesses.

Authority order during implementation: current user instruction, reviewed spec, issue drafts, PRD, existing code conventions.

## Context Hygiene

- Prefer subagents for broad exploration, artifact drafting, and reviews.
- Keep run state in breadcrumbs and local artifact paths.
- If context grows, write or refresh a local run handoff under `docs/oh-my-ai-skill-runs/YYYY-MM-DD-kebab-topic/` and continue immediately from it.
- Do not ask the user to compact or send a resume prompt.

Run handoffs should contain only recovery-critical facts: source artifacts, resolved decisions, assumptions, council summaries, review findings/resolutions, issue queue status, verification status, blockers, and next safe action.

## Ralph Loop

Ralph loop means repeat until all resolvable issue drafts are complete:

`AFK` means an issue draft can be implemented without further human input. `HITL` means the draft names a required human decision or review; once that decision or review is resolved and recorded, the draft is eligible for implementation.

1. Select the next unblocked AFK or resolved HITL issue in dependency order. Skip unresolved HITL issues until their required human decision or review is resolved and recorded.
2. Load reviewed spec plus the issue draft.
3. Investigate prior art with subagents when useful.
4. Implement the smallest safe vertical slice.
5. Review diff, then run narrowest useful verification.
6. Fix failures with bounded retries; require new evidence before repeating the same attempt.
7. Update local issue checkboxes only after acceptance criteria are verified.
8. Update breadcrumb or run handoff before context-heavy transitions.
9. Continue to the next unblocked AFK or resolved HITL issue without asking.

Stop only for true no-safe-local-path blockers: destructive action, secrets/credentials, external publishing/spend/deploy, conflicting requirements, repeated verification failure after distinct fixes, or scope expansion that cannot be reversed cheaply.

## Idle Recovery

If the run stalls, restart from the breadcrumb/run handoff and ask internally: what is the next unblocked acceptance criterion? Continue from that slice. Do not wait for user input unless the blocker list says no safe local path exists.
