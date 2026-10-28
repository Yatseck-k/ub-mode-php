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

### Optional CDMU checks for PHP

- Run the project's tests in its normal environment first. If `vendor/bin/ub-php` is installed, use it from the project root in that same environment; do not install CDMU automatically or make it a prerequisite for the workflow.
- Run `vendor/bin/ub-php doctor --format json`. If healthy, use `vendor/bin/ub-php check --changed --format json` for changed PHP files. If Git is unavailable or the directory is outside Git, omit `--changed` and analyze configured source directories (`--changed` outside a Git repository exits 3 with "--changed requires a Git repository"). A full scan with no PHP files in the configured source directories also exits 3. An empty changed report does not assess the whole project; use a full scan when broader evidence is needed.
- Read JSON `entries` and `candidates` along with the exit code. Do not parse the human text report or progress bar. A zero exit code can still include unknown coverage, duplication warnings, or CRAP violations when `quality.fail_on` disables that gate.
- Exit code `2` means a configured known CRAP gate failed; `3` means analysis failed; `4` means invalid usage. Correct a mistaken invocation when possible. If CDMU is absent or cannot run, continue with project tests and manual review, state why measurements were unavailable, and never describe a tool failure as a successful check.
- CDMU supplies static CRAP/duplication checks, an optional Infection adapter, and offline UML. Ordinary `check` and `uml` do not run project code. Explicit `mutate` runs tests with their normal side effects; do not add it automatically to every change. The PHP 8.2+ Composer package remains optional; installation examples are in the companion README.

### Crapper

- Inspect `entries[].exceeded_threshold` and the project's configured threshold and `quality.fail_on`. The default CRAP threshold is 10; known values at or above the active threshold are findings. Simplify branching, improve tests, or both; do not lower thresholds to conceal findings.
- `coverage: null` and `crap: null` mean unknown, not 0% or a verified pass. Use an existing Clover report generated by the project's tests when available. Without CDMU or coverage, review complexity and test gaps manually and state that CRAP was not measured.

### Dryer

- Review JSON `candidates` for structural similarity. For a stricter review when warranted, run `vendor/bin/ub-php dry --changed --threshold 0.8 --format json`; omit `--changed` outside Git. Candidates are warnings: compare meaning before extracting shared code.
- Without a duplication tool, inspect changed code for repeated structure and describe findings without a similarity score.

### Mutator

- If the project has a suitable mutation tool, use it when the task warrants the cost. Add tests for uncovered behavior, investigate surviving mutants, and remove code shown to be dead.
- When CDMU is installed and mutation evidence is warranted, probe `vendor/bin/ub-php doctor --tools --format json`. Use `vendor/bin/ub-php mutate --changed --format json` only when additional test execution is within scope and the supported Infection version, coverage driver and process control are available. Omit `--changed` outside Git. An older CDMU may not support `--tools`; continue with other available tools rather than making an upgrade mandatory.
- Read `summary`, `mutants`, `status`, and `exit_code` with the actual process exit code. Killed, survived, uncovered, timeout, error, syntax_error, ignored and skipped are distinct outcomes. MSI and covered MSI differ. A known MSI below the supplied/configured minimum returns 2. Null MSI without a minimum gives WARN/0; with `--min-msi` or `mutator.min_msi`, it returns the full mutate envelope with FAIL/3 and `gate_evaluated: false` in JSON stdout and an identical retained `failure.json`, including an empty changed scope. Baseline/parser/I/O errors before a valid result may instead have empty stdout and stderr diagnostics. Engine/baseline/report failures also return 3. Preserve required thresholds; do not turn unavailable measurements into a pass.
- A failed or unevaluated run can leave the previous published `mutate.json` intact. Read current diagnostics and the reported failure/log path; do not present that old snapshot as the result of the latest invocation.
- Fingerprints track source/config/test inputs and automatically exclude declared PHPUnit logging/coverage/cache outputs and reports. When needed, describe additional local inputs with `mutator.input_paths` and generated test data with `mutator.input_exclude`; source and mandatory configs remain tracked. Do not exclude genuine inputs to hide changes. Report history defaults to five generations per M/U tool and five failed runs; 0 disables the corresponding limit. The configuration hash tracks mutation input/selection settings, not raw YAML, presentation, reports/retention, CRAP/DRY thresholds, MSI minimum, threads or overall timeout. These operational edits preserve measurements; source/test/bootstrap/engine-config or input-rule edits require fresh evidence. Changing reports.directory changes lookup without moving snapshots. Preserve project settings; see the companion README for examples.
- Otherwise, examine likely test gaps manually and report mutation analysis as unavailable. Do not install Infection automatically.

### Refactoring and Visualization

- Pin behavior, change structure, and rerun the same checks. For PHP structure when useful, run `vendor/bin/ub-php uml --format json`; its report directory contains an offline HTML viewer and SVG. It does not run mutation tests or edit source. Use `--namespace` to focus the view; `--changed` is not supported for UML because it needs a complete graph.
- Treat `mutation_overlay.status` stale/unavailable and unknown CRAP as missing evidence. The viewer is a static snapshot, so regenerate it after changes. It cannot infer DI/magic/dynamic dependencies or rewrite architecture. UML remains optional, and exit 0 reports successful generation rather than a quality gate.
- Nested closure/arrow mutants count once in their class and containing method; a unique innermost unit owns them. Equal/ambiguous spans keep null unit_id and diagnostics, with only unambiguous class/method attribution. Do not invent a precise binding from line-only coordinates.
- Duplicate FQCNs produce WARN and an `ambiguous` node with all definitions; its metrics remain unknown and ambiguous outgoing relations are omitted with diagnostics. Review the definitions without choosing one as authoritative. Missing/unreadable/malformed optional Clover coverage gives unknown metrics and diagnostics rather than discarding the graph; a valid explicitly configured external coverage symlink is supported.

## Practice

### Keep a Log

- Record significant actions and their reasons. Report tests, measured findings, manual observations, and unavailable checks separately.

### Guard the Context Window

- Keep working context focused on the current task and preserve the facts needed to continue.

### Worktree Cleanup

- Remove only files or worktrees confirmed safe to discard. Leave uncommitted work for its owner to decide.

### Commit and Push

- Make focused commits according to repository rules, recording why the change was made and how it was checked. Push or publish only when authorized for that repository.
