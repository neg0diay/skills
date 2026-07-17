---
name: to-spec
description: Turn the current conversation into a spec and publish it to the project issue tracker — no interview, just synthesis of what you've already discussed.
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec (you may know this document as a PRD). Do NOT start by treating this as a pure requirements-capture exercise: the goal is to research the affected area, propose a concrete solution, validate it with the user, and freeze the solution shape before implementation.

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-matt-pocock-skills` if not.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Research the affected area deeply enough to understand how the current implementation works, where the integration points are, and what kind of system you are changing (backend, frontend, fullstack, library, infra/platform, data/ML, CLI, or mixed). Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Produce an **Architecture Proposal** before you write the final spec. The proposal must describe:

- The current codebase area you investigated
- The proposed design
- How the design integrates into the existing codebase
- Relevant alternatives considered and why this option is preferred
- Risks or open questions that must be resolved before freezing the solution

Stop and get the user's approval on the proposal before proceeding.

3. After the proposal is approved, run a separate **Validation** pass on that proposal. Validate that:

- The affected modules and integration points are fully covered
- The main system flow or user flow is complete
- The relevant contracts, state transitions, data/persistence changes, and operational effects for this type of system are accounted for
- It is clear which existing modules must remain unchanged
- The implementation can proceed without further architecture research

For backend projects, validate data-layer and migration conventions explicitly. In particular:

- For Golang projects, check whether `sqlc` is present in the repo or build workflow
- If `sqlc` is present, any required database-access-layer changes must be designed to flow through SQL changes plus `sqlc` code generation, not manual edits to generated access code
- Any new schema work must be planned as new forward migrations; do not rely on editing old migrations that may already be applied in developer databases

Record the result as one of: `Approved`, `Approved with constraints`, or `Not approved`.

If the result is `Not approved`, go back to the proposal stage instead of writing the final spec.

4. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

5. Write the spec using the template below, then publish it to the project issue tracker. The spec must freeze the final solution shape so the implementation agent does not need to reinterpret the architecture. Apply the `ready-for-agent` triage label - no need for additional triage.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## Architecture Proposal

### Current Area

Which part of the current codebase was investigated and why it is the relevant area for this change.

### Proposed Design

The concrete design being proposed.

### Integration Into Existing Codebase

How this design fits into the current modules, entry points, flows, and extension points.

### Alternatives Considered

Short list of meaningful alternatives and why they were not chosen.

### Risks / Open Questions

Anything that had to be resolved before the design could be frozen.

### Proposal Approval

Whether the user approved the architecture proposal.

## Validation Report

### Validation Status

`Approved`, `Approved with constraints`, or `Not approved`.

### Coverage Check

Whether all affected modules, layers, and integration points are covered.

### Flow Completeness Check

Whether the main system flow or user flow is fully described.

### Contract / State Impact Check

Whether the relevant contracts, state transitions, persistence changes, API/event/job changes, UI state changes, deployment/config changes, or other system-type-specific impacts are accounted for.

For backend work, this must also call out whether database-layer changes go through framework/tooling conventions such as `sqlc`, and whether migration changes are additive new migrations rather than edits to previously applied ones.

### Constraints Carried Forward

Any constraints discovered during validation that must bind implementation.

## Final Solution Shape

This section freezes the implementation shape after proposal approval and validation.

### Modules That Must Be Changed

- ...

### Modules That Must Be Added

- ...

### Modules That Must Remain Unchanged

- ...

### System or User Flow After Change

Describe the primary flow after the change. Use the flow that fits the system type: request/event/job flow for backend, user and client-state flow for frontend, end-to-end flow for fullstack, and equivalent flow shapes for other system types.

### Codebase Integration Notes

Explain how the frozen design is wired into the current codebase and where the main integration points are.

### System-Type-Specific Impact

Include only the categories relevant to this change. For categories that do not apply, write `Not applicable`.

- Data and migration plan
- API / event / job / contract changes
- UI surface affected
- State / data flow changes
- Routing / navigation changes
- Deployment / environment / config changes
- Backward compatibility and rollout / rollback expectations
- Public interface changes

For Golang backend projects, explicitly state whether `sqlc` is present. If it is, the frozen solution shape must say that DB-layer changes are made via SQL source changes and `sqlc` CLI generation, not manual edits to generated code. For any schema evolution, require new forward migrations rather than modifications to old migrations that may already have been applied.

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

## Implementation Constraints

This section is binding on the implementation agent.

- Do not perform additional architecture research.
- Do not reinterpret the intended solution.
- Do not introduce new modules, migrations, interfaces, flows, contracts, or infrastructure changes unless they are explicitly listed in this spec.
- If this spec is insufficient or contradictory, stop and ask for clarification instead of designing a new solution.
- For Golang backend projects, if `sqlc` is present, do not manually edit generated DB-access code; make the required SQL changes and regenerate through the `sqlc` CLI workflow used by the project.
- Do not modify old migrations for new work. Create new forward migrations instead.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

</spec-template>
