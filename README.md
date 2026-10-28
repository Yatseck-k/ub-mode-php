# ub-mode-php

[Русская версия](README.ru.md)

An independent agent skill for disciplined code changes. It works with the target project's tests and code review alone. Optional analyzers add reproducible measurements when installed. The skill itself has no runtime dependencies. Its optional PHP analyzer, `ub-cdmu-php` (CDMU), is a Composer package for PHP 8.2+ and requires neither Python nor Docker. The skill is invoked as `ub-mode`.

The skill follows the upstream sequence: **First Make it Work** (small changes, TDD, design and boundaries), **Then Make it Right** (root causes, behavior tests, complexity, duplication, mutation and refactoring), and **Practice** (work log, context, cleanup and commits). Each principle remains actionable without an analyzer.

## Install the skill

From this checkout, copy the skill to the directory used by your agent. For Codex:

```sh
mkdir -p ~/.codex/skills/ub-mode
cp SKILL.md ~/.codex/skills/ub-mode/SKILL.md
```

For Claude Code or Cursor, copy it as `ub-mode/SKILL.md` under that agent's skills directory. Invoke `$ub-mode` or `/ub-mode` according to the agent. After editing this repository's skill, repeat the copy to update an installed instance.

## Optional CDMU

The agent first runs the project's tests and reviews changed code. If CDMU is absent or cannot run, it continues with tests and manual review and reports which measurements were unavailable. CDMU is never installed automatically.

Install it in the PHP project yourself. Until it is on Packagist, use GitHub or a local checkout (the path must exist in the environment where Composer runs, including containers):

```sh
composer config repositories.ub-cdmu-php vcs https://github.com/Yatseck-k/ub-cdmu-php
composer require --dev yatseck-k/ub-cdmu-php:dev-main
# or, from a local checkout:
composer config repositories.ub-cdmu-php path ../ub-cdmu-php
composer require --dev yatseck-k/ub-cdmu-php:@dev
```

New to CDMU? Read its [quick guide](https://github.com/Yatseck-k/ub-cdmu-php/blob/main/docs/GUIDE.md). The [reference guide](https://github.com/Yatseck-k/ub-cdmu-php/blob/main/docs/README.md#cli) has every option, exit code and report contract.

## What the agent runs

From the project root, after the project's tests:

```sh
vendor/bin/ub-php doctor --format json
vendor/bin/ub-php check --changed --format json
# Stricter duplication review when warranted:
vendor/bin/ub-php dry --changed --threshold 0.8 --format json
```

Outside Git, omit `--changed` (with it, CDMU exits 3). An empty changed scan does not assess the whole project. The agent decides from JSON (`entries`, `exceeded_threshold`, `coverage`, `candidates`) together with the exit code, not from the human-readable text:

| Exit code | Meaning |
| --- | --- |
| `0` | Passed; may include unknown coverage, duplicate warnings, or CRAP violations when `quality.fail_on` disables the gate |
| `2` | Known CRAP score at or above the threshold (default 10), or known MSI below the minimum |
| `3` | Analysis, environment, configuration or report error (also an empty full scan) |
| `4` | Invalid usage |

Missing Clover coverage is `unknown`, never 0% and never a verified pass. DRY candidates are warnings. Thresholds are never lowered to hide findings.

## Mutation and UML

```sh
vendor/bin/ub-php doctor --tools --format json
# Runs the project's tests with their normal side effects; use only when the task warrants it:
vendor/bin/ub-php mutate --changed --min-msi 80 --format json
# Static analysis, no target code execution:
vendor/bin/ub-php uml --format json
# Open .metrics/uml.html in a browser; no server is needed.
```

`mutate` supports Infection **0.29.14** (verified with PHP 8.2, PHPUnit 11.5 and Xdebug) and needs POSIX/PCNTL on Linux/macOS. Without these, the agent reports mutation analysis as unavailable.

- A known MSI below the minimum exits 2. A null MSI without a minimum is WARN/0; with `--min-msi` or `mutator.min_msi` it is FAIL/3, because the required gate could not be evaluated. In that case stdout holds the full JSON envelope with `gate_evaluated: false`. Other engine, baseline or I/O failures exit 3 and may leave stdout empty, so check the exit code before parsing.
- A failed run can leave an older `mutate.json` in place; it is not the result of the latest run.
- Killed, survived, uncovered, timeout, error, syntax_error, ignored and skipped mutants are distinct, and MSI differs from covered MSI.
- Mutation fingerprints track source, test and configuration inputs. Use `mutator.input_paths` for extra real inputs and `mutator.input_exclude` for generated test data such as `tests/tmp`; never exclude genuine inputs to hide changes.
- Report history defaults to five generations per tool and five failed runs; `0` removes a limit.

UML is a static snapshot: classes, relations, current CRAP and, when valid, a mutation overlay. Stale or unavailable overlays and unknown CRAP are missing evidence. Duplicate class names give WARN and an `ambiguous` node. UML exit `0` means generation succeeded, not that a quality gate passed. `--changed` is not supported.

## Provenance

This skill follows the three-stage structure of [unclebob/ub-mode](https://github.com/unclebob/ub-mode). Its instructions were adapted for optional tools and PHP project workflows; the original skill and its companion [Crapper](https://github.com/unclebob/crapper), [Dryer](https://github.com/unclebob/dryer), [Mutator](https://github.com/unclebob/mutator), and [UML Viewer](https://github.com/unclebob/uml-viewer) are independent upstream projects.
