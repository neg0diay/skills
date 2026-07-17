---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If both tickets and a spec exist, read both before starting. Treat the spec's approved architecture proposal, validation result, final solution shape, and implementation constraints as the source of truth.

Repository scope is part of the source of truth. Only change repositories or git submodules that the spec or ticket explicitly marks as in scope.

Do not perform additional architecture research or redesign the solution during implementation. Do not introduce new modules, migrations, interfaces, flows, contracts, or infrastructure changes unless they are explicitly called for by the spec or ticket.

If the task description is insufficient, contradictory, or missing a frozen solution shape, stop and ask for clarification instead of inventing a new design.

If repository scope is missing or ambiguous, stop and ask for clarification before making changes.

For backend work, preserve the data-layer and migration rules in the spec or ticket. In particular:

- For Golang projects, check whether `sqlc` is present in the repo or required by the spec/ticket. If it is, make DB-layer changes through SQL source updates plus the project's `sqlc` CLI generation workflow, not by manually editing generated DB-access code.
- For new schema work, create new forward migrations. Do not modify old migrations for new tasks, because they may already have been applied in developer databases.

Use /tdd where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work.

Do not commit automatically. After implementation and review, stop and ask the user whether they want the changes committed. The default is to leave the work uncommitted for user review.
