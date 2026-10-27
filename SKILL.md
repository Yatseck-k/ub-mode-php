---
name: ub-mode
description: Follow the reduced Make it Work, Make it Right, Practice principles for code changes. Use when the user invokes ub-mode or asks for these principles; use available quality tools without making them prerequisites.
---

# ub-mode

Apply these principles to code changes in any language. Follow the user's scope and repository rules. The workflow works with project tests and code review alone. Optional tools provide measured evidence; never invent a metric when a tool or coverage report is missing.

## First Make it Work

### Laziness Protocol

- Keep each decision in one place and make the smallest useful change.

### Foundational Thinking

- Settle the data shape before implementing behavior. Do early work that makes later steps easier.
- If concurrent writers could interfere, give them separate state or one explicit owner.

### TDD

- Where practical, write a behavior test that fails for the expected reason, make it pass, then improve the design.
- Skip a test that would only repeat the implementation or could pass with no useful behavior.

### Redesign From First Principles

- For a new requirement, consider the design you would choose with that requirement present from the start. Deliver the change in small pieces.

### Attack the Premise

- If two fixes based on the same assumption fail, state that assumption and check who or what actually owns the responsibility before trying another fix.

### Subtract Before You Add

- Remove unnecessary branches, state, or duplication before introducing more machinery.

### Outcome-Oriented Execution

- Define a result you can verify. Keep intermediate steps reversible when they temporarily break behavior.

### Build the Lever

- If a repeated task is unclear, perform one representative case first, then make a helper or script reproduce it.

### Architecture

#### Model the Domain

- Model domain values so invalid states are hard to express; keep a small glossary only when terms need clarification.

#### Boundary Discipline

- Validate outside data once at the boundary. Keep core calculations separate from I/O.

#### Type System Discipline

- Use types to represent validated values and make invalid values hard to construct. Do not silence type errors without fixing their cause.

#### Make Operations Idempotent

- Make retryable operations end in the same state after repetition or interruption.

#### Separate Before Serializing Shared State

- Share mutable state only when participants genuinely need the same state, and give it one writer.

## Then Make it Right

### Fix Root Causes

- Reproduce a failure and correct its cause. A guard that merely hides the symptom is insufficient.

### Sequence Work into Verifiable Units

- Complete and check one small unit before beginning the next.

### Test Behavior, Not Implementation

- Call the code as its user would and assert an observable result. Reject a test that would still pass if the implementation did nothing useful.

### Crapper

- Review changed functions for complexity and test coverage. In a PHP project where `command -v ub-php` succeeds, run project tests first, then `ub-php check --changed --format json`. Use a full check when project-wide evidence is needed.
- A known CRAP score must stay below 10 by simplifying code, improving tests, or both. Missing Clover coverage is unknown, not 0%. Without a measurement tool, review branching manually and report that CRAP was not measured.

### Dryer

- When `ub-php` is available for PHP, review `ub-php dry --changed --threshold 0.8`. Candidates are warnings: compare meaning before extracting shared code.
- Without a duplication tool, inspect changed code for repeated structure and describe findings without a similarity score.

### Mutator

- If the project has a suitable mutation tool, use it when the task warrants the cost. Add tests for uncovered behavior, investigate surviving mutants, and remove code shown to be dead.
- Otherwise, examine likely test gaps manually and report mutation analysis as unavailable. The current `ub-cdmu-php` CLI does not implement mutation testing.

### Refactoring and Visualization

- Pin behavior, change structure, and rerun the same checks. Use a UML viewer when available and useful for architecture; it is not required to complete the workflow.
- Keep quality failures distinct from tool failures: `ub-php` exit code 2 means a known CRAP threshold failure; 3 or 4 means analysis or usage failed. Do not lower thresholds to make a check pass.

## Practice

### Keep a Log

- Record significant actions and their reasons. Report tests, measured findings, manual observations, and unavailable checks separately.

### Guard the Context Window

- Keep working context focused on the current task and preserve the facts needed to continue.

### Worktree Cleanup

- Remove only files or worktrees confirmed safe to discard. Leave uncommitted work for its owner to decide.

### Commit and Push

- Make focused commits according to repository rules, recording why the change was made and how it was checked. Push or publish only when authorized for that repository.
