---
name: council
description: Structured multi-role agent council for hard decisions, hostile plan reviews, architecture trade-offs, contradictions, and risk analysis. Use when a question needs several perspectives, the user asks for a council/panel/multiple agents/red team, says debate but needs bounded synthesis, requirements conflict, or a planning/implementation gate needs evidence-backed review before continuing.
---

# Council

Run a small evidence-backed council, then synthesize one decision. Subagents advise; main agent owns judgment, action, and user-facing output.

## When To Use

- Hard product, architecture, integration, data, UX, security, or rollout choices.
- Contradictory requirements, repo evidence, docs, tests, or stakeholder goals.
- Review gates for PRDs, specs, issue drafts, plans, diffs, or implementation strategy.
- User says `council`, `debate this`, `panel`, `multiple agents`, `red team`, or `get perspectives`; route debate wording into bounded council with one synthesis owner.

Do not use council for one-line answers, obvious edits, routine formatting, or questions where direct repo evidence fully resolves the answer.

## Core Rule

Council is advisory. Do not outsource final reasoning. Gather perspectives, compare evidence, resolve disagreements, then choose the smallest safe next action.

## Default Roles

- **Explorer**: finds repo facts, docs, code paths, tests, prior art, and source citations.
- **Oracle**: evaluates architecture, domain boundaries, long-term maintainability, and conceptual fit.
- **Skeptic**: attacks assumptions, hidden costs, failure modes, security risks, and scope creep.
- **Builder**: estimates implementation path, migration cost, test impact, and operational complexity.
- **Creative Challenger**: generates alternate framings so the chosen path is chosen despite alternatives, not because no alternatives were considered.

Adjust roles to the decision. For security-heavy work, add **Threat Modeler**. For UX work, add **User Advocate**. For release work, add **Operator**.

## Hyperplan Mode

When user asks for `hyperplan`, hostile planning, cross-critique, or a plan that must survive strong opposition, prefer the dedicated `hyperplan` skill. If staying inside `council`, switch to hostile mode:

- **Pragmatist Skeptic** attacks complexity and asks what can be deleted.
- **Integration Tester** attacks missing edge cases, state transitions, and blast radius.
- **Researcher** attacks unsupported claims and demands citations.
- **Architect Strategist** attacks coupling, bad boundaries, and hidden technical debt.
- **Creative Challenger** attacks first-thought framing and proposes concrete alternatives.

Hostile mode is not consensus. Distill only findings that survive evidence and cross-attack.

## Workflow

1. Define the decision in one sentence, including what must be chosen and what would change downstream.
2. Gather baseline evidence first: relevant docs, code, tests, logs, specs, and user constraints.
3. Assign roles with tight prompts. Ask each role for recommendation, evidence, risks, example, and downstream effect.
4. Run roles in parallel when available; otherwise simulate sequentially with the same output contract.
5. Compare role outputs. Mark consensus, conflicts, missing evidence, and assumptions.
6. If conflicts are material, run one focused second round only on the disputed point.
7. Synthesize final decision with rationale, rejected options, risks, and next action.
8. Record decision in the right artifact when durable: PRD, spec, ADR, decision tree, run handoff, or breadcrumb.

## Output Contract

Use this shape for council notes and summaries:

```md
## Council Decision

Decision: <one sentence>
Recommendation: <chosen path>
Why: <evidence-backed rationale>
Evidence:
- <path/doc/source> - <fact>
Risks:
- <risk> - <mitigation>
Rejected:
- <option> - <reason>
Next action: <concrete step>
```

For internal role results, keep output compact:

```md
Role: <name>
Recommendation: <path>
Evidence: <sources/facts>
Risks: <top risks>
Example: <concrete scenario>
Downstream effect: <what changes>
```

## Decision Quality Checks

- Evidence beats confidence. Cite files, docs, commands, or observed behavior where possible.
- Disagreement is signal. Do not average positions; identify which assumption differs.
- Prefer reversible decisions when evidence is weak.
- Escalate to user only when unresolved choice materially changes behavior, data shape, external contracts, security posture, cost, or irreversible scope.
- Do not hide uncertainty. Label assumptions and add follow-up verification.
- If every role agrees too quickly, run one hostile challenge against the consensus.

## Subagent Use

Use subagents when available and context would benefit from separation. Run independent roles in parallel when they can inspect different angles. If subagents are unavailable, simulate roles sequentially in the main thread and keep the same output contract.

## Common Pitfalls

- Do not let council become a long transcript. Summarize findings and preserve sources.
- Do not run repeated rounds without new evidence or a narrowed dispute.
- Do not ask every role the same vague question; give each role a specific lens.
- Do not treat majority vote as correctness. Resolve by evidence, constraints, and risk.
- Do not let creative alternatives expand implementation scope without passing the same evidence and cost checks.
