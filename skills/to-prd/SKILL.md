---
name: to-prd
description: Create a product requirements document from the current conversation, decisions, constraints, and user goals. Use when the user asks for a PRD, product brief, requirement document, or captured product plan.
---

This skill takes the current conversation context and codebase understanding and produces a PRD. Do not run a broad interview; synthesize what you already know and ask only the narrow checks required below unless a strict hands-off pipeline supplies assumed approval.

## Local-only rule

All output is local by default. Do NOT create, update, push, or publish anything to GitHub, GitLab, issue trackers, or other remote services unless the user explicitly asks for that specific remote action.

Issue tracker and triage label vocabulary are only needed if the user explicitly asks you to publish remotely. For local PRD files, do not run tracker setup.

## Recovery breadcrumbs

For non-trivial PRD creation, load `agent-breadcrumbs` and update `.agents/state/breadcrumbs/<agent-id>.md` after context gathering, before writing the PRD, and after saving it locally so `connection-recovery` can reconstruct progress after interruption.

## Local file storage

Store PRDs in the current repo, not in global agent config. If the repo already has a PRD or planning-doc convention, follow it. Otherwise create files lazily under `docs/prds/` using `YYYY-MM-DD-kebab-title.md`.

Use lowercase ASCII kebab-case for filenames. Start the file with an H1 title, then include the PRD template. If the PRD came from an issue, spec, or conversation reference, include that source in `Further Notes` instead of encoding it in the filename.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the PRD, and respect any ADRs in the area you're touching.

2. Sketch out the major modules you will need to build or modify to complete the implementation. Actively look for opportunities to extract deep modules that can be tested in isolation.

A deep module (as opposed to a shallow module) is one which encapsulates a lot of functionality in a simple, testable interface which rarely changes.

Check with the user only when module or test-scope choices materially change behavior, risk, or cost. In a strict hands-off pipeline such as `oh-my-ai-skill`, use the pipeline's assumed approval instead, record that assumption in `Further Notes`, and continue only if there are no unresolved blocking questions.

3. Write the PRD using the template below, then save it locally using the storage rules above. If the user explicitly asks you to publish it remotely, apply the `ready-for-agent` triage label - no need for additional triage.

<prd-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this PRD.

## Further Notes

Any further notes about the feature.

</prd-template>
