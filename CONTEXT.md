# Adnan AI Skills

This context defines the language used for this repository's agent-skill workflows.

## Language

**Oh My AI Skill**:
An end-to-end agent-skill workflow that orchestrates docs-aware grilling, subagent analysis, planning artifacts, review gates, context hygiene, and implementation loops.
_Avoid_: Oh My OpenCode, Oh My OpenAgent when referring to this repo-local skill.

**Subagent**:
A delegated agent running within the same user session, used for bounded analysis, artifact creation, review, or implementation support without interrupting the main workflow.
_Avoid_: Agent when that could imply a separate external session.

**Ralph Loop**:
A self-continuing implementation loop that selects the next unblocked issue, implements it, reviews it, verifies acceptance criteria, updates local progress, and repeats until all resolvable issues are complete.
_Avoid_: One-shot implementation, manual checkpoint loop.

**Council**:
A short-lived group of role-focused subagents that analyze a hard decision from different angles before the main agent synthesizes one decision.
_Avoid_: Debate when referring to unbounded back-and-forth without a synthesis owner.

**UFW**:
Ultra Focused Work; a bounded, high-intensity agent execution loop with a clear objective, compact queue, verification after each slice, and interruptions only for true blockers.
_Avoid_: Ubuntu firewall when discussing this repo's workflow skill.
