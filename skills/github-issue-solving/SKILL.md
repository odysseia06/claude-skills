---
name: github-issue-solving
description: Validate and resolve GitHub issues end to end. Use when work starts from a GitHub issue, bug report, regression, failing reproduction, feature request with acceptance criteria, or linked ticket and the goal is to determine whether it is real, implement the smallest correct change, prove it with tests, explain the fix clearly, and ship it. Trigger whenever the user references an issue number, issue URL, bug ticket, repro steps, acceptance criteria, or asks to fix, resolve, close, or implement a GitHub issue. Do not use for PR-only review, generic brainstorming, or unrelated repo exploration.
---

# GitHub Issue Solving

Resolve issues with evidence first, the smallest correct change second, and complete verification before claiming success.

If GitHub tooling is available, read the issue, comments, linked pull requests, and referenced commits before editing code. If it is not available, use the issue text the user supplied and local repository context.

## Core Rules

- Do not trust the issue description blindly. Validate it against the current codebase.
- Classify the issue before coding: confirmed bug, confirmed feature gap, already fixed, invalid, duplicate, or out of scope.
- Prefer the narrowest correct fix over the most general design.
- Reuse existing methods, classes, helpers, and test patterns whenever possible.
- Do not introduce new files, abstractions, dependencies, or helper layers unless the existing code cannot absorb the change cleanly.
- Add or update tests that prove the issue and the fix.
- Do not mix unrelated cleanup with the issue fix.
- Do not claim the issue is solved while relevant tests or checks are failing.
- Do not create extra report files unless the repository already requires them. Deliver the explanation in the final response by default.

## Workflow

### 1. Understand the Issue

Extract and restate:

- the user-visible problem or requested behavior
- expected behavior vs actual behavior
- reproduction steps, acceptance criteria, and constraints
- the likely subsystem, files, or ownership area

If the issue is vague, inspect nearby code, tests, history, and docs before asking follow-up questions.

### 2. Verify That the Issue Is Real

Use evidence, not intuition. Validate with the strongest available proof:

- reproduce the bug locally
- run the existing failing test, if one exists
- inspect the current implementation and compare it with the issue claim
- check config, feature flags, permissions, versions, and environment assumptions
- compare behavior with docs, specs, or adjacent tests

Classify the result:

- `confirmed-bug`: current behavior is wrong
- `confirmed-feature-gap`: accepted behavior is missing
- `already-fixed`: current branch already satisfies the issue
- `invalid-or-duplicate`: issue is incorrect, superseded, or not actionable

If the issue is not valid, stop implementation and report the evidence clearly instead of forcing a code change.

### 3. Design the Smallest Correct Change

Before editing, find the closest existing implementation point and reuse it.

Prefer:

- extending an existing branch, validator, parser, service, or component
- tightening an existing test file instead of inventing a new test harness
- following existing naming, error-handling, and data-flow patterns

Avoid:

- speculative abstractions for future needs
- new classes or files for one-off logic
- new dependencies when the standard library or current stack already solves the problem
- wide refactors hidden inside a bug fix

If adding a new file, helper, or dependency is truly necessary, justify it in the final explanation.

### 4. Prove the Problem With a Test

For bugs, write or update a test that fails before the fix.

For feature issues, write tests that encode the acceptance criteria before or alongside implementation.

Test guidance:

- mirror the nearest existing test style
- test behavior, not internal call sequences
- cover the exact issue plus one high-signal edge case
- keep the test as small and direct as possible

If the repository has no realistic place to add a test, explain why and use the strongest available alternative verification. Do not silently skip this decision.

### 5. Implement Incrementally

Change the narrowest surface that fixes the issue.

When editing:

- preserve unrelated behavior
- keep the code consistent with the surrounding module
- prefer modifying existing methods or classes over introducing parallel paths
- keep the repository in a working state after each increment

When the issue crosses multiple layers, implement a thin end-to-end slice rather than a broad rewrite.

### 6. Verify Completely

Run verification in this order:

1. the reproduction test or focused failing case
2. the impacted test file or suite
3. project-standard checks for changed code such as lint, typecheck, or build
4. the full relevant test suite, and the full project suite when feasible

Success means the new proof passes, surrounding behavior still passes, and no required project checks are left red.

If a full suite cannot be run, say exactly why. Do not describe the issue as fully resolved without being explicit about that limitation.

### 7. Review for Simplicity and Scope

Before finalizing, ask:

- Is there a simpler fix that achieves the same result?
- Did the change stay inside the issue scope?
- Did it reuse existing methods, classes, and patterns?
- Did it avoid unnecessary files, helpers, imports, and dependencies?
- Would another engineer understand the fix quickly without extra explanation?

Tighten the implementation until the answer is yes.

### 8. Prepare the Delivery

The final response must explain both the issue and the fix. Include:

- issue summary
- validity result and how it was verified
- root cause or missing behavior
- concise solution summary
- files changed
- key methods, functions, classes, or patterns touched or reused
- new or changed imports and libraries
- tests and checks run, with outcomes
- residual risks or follow-up work, if any
- commit message
- push status

If the issue was invalid or already fixed, say that explicitly and do not pretend code changes were needed.

## Final Response Template

Use this structure unless the user requests a different format:

```markdown
Issue
- What the issue claimed and what was expected

Validation
- How the issue was verified or invalidated

Root Cause
- The actual failure point, or why the issue was invalid

Solution
- What changed and why this was the smallest correct fix

Code Details
- Files changed
- Key methods/functions/classes touched or reused
- New or changed imports/libraries

Verification
- Tests and checks run
- Final result

Git
- Commit: <type(scope): concise description (#issuenumber)>
- Push: pushed / not pushed, with reason if not
```

## Git Discipline

Before committing:

- stage only the files that belong to the issue
- review the staged diff
- make sure the verification story is clean

Use a conventional commit line that is minimal but informative, add the issue number in paranthesis at the end of the line:

- `fix(auth): prevent duplicate token refresh on retry (#42)`
- `fix(api): handle empty label filters in issue search (#31)`
- `feat(importer): support milestone mapping from issue payloads (#12)`

Add a short body only when it clarifies why the change exists or what verification was added.

Push only after the issue is verified and the commit contains only intentional changes.
