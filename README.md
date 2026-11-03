# ub-mode-php

**A PHP-focused AI agent skill for disciplined, test-driven code changes.**

[Русский](README.ru.md) · [Skill instructions](SKILL.md) · [Original ub-mode](https://github.com/unclebob/ub-mode)

![PHP](https://img.shields.io/badge/Focus-PHP-777BB4?logo=php&logoColor=white) ![AI agents](https://img.shields.io/badge/AI-agent%20skill-374151)

## Overview

`ub-mode-php` adapts the three-stage workflow from [Uncle Bob's ub-mode](https://github.com/unclebob/ub-mode) for PHP projects and coding agents. **The skill works without any external analyzer or runtime dependencies.** Its installed name is `ub-mode`.

| Stage | Focus |
| --- | --- |
| **First Make It Work** | Small changes, test-driven development, behavior and boundaries |
| **Then Make It Right** | Root causes, code review, complexity, duplication, mutation testing and refactoring |
| **Practice** | Work log, context management, cleanup and commit discipline |

## Installation

Copy the single [SKILL.md](SKILL.md) file into your coding agent's skill directory. For Codex:

```bash
mkdir -p ~/.codex/skills/ub-mode
cp SKILL.md ~/.codex/skills/ub-mode/SKILL.md
```

For other agents supporting `SKILL.md`, use their corresponding skills directory. Invoke the installed skill using the agent's supported syntax (for example, `$ub-mode`).

## Optional PHP quality analysis

The independent [ub-cdmu-php](https://github.com/Yatseck-k/ub-cdmu-php) Composer package adds reproducible CRAP, duplicate-detection, mutation-testing and UML measurements. **It is optional and never installed automatically.** The CLI currently requires **PHP 8.4.27+ (<8.5) or PHP 8.5.12+ (<9.0)**; the agent skill itself does not.

To install CDMU directly from GitHub in a compatible target project:

```bash
composer config repositories.ub-cdmu-php vcs https://github.com/Yatseck-k/ub-cdmu-php
composer require --dev yatseck-k/ub-cdmu-php:dev-main
```

After running the project's own tests, an agent can inspect changes with:

```bash
vendor/bin/ub-php doctor --format json
vendor/bin/ub-php check --changed --format json
vendor/bin/ub-php dry --changed --threshold 0.8 --format json
```

`--changed` requires Git; an empty changed-file scan does not assess the entire project. The agent must evaluate both exit codes and structured report fields. Missing coverage is **unknown**, not a verified pass. Duplication results are candidates for review.

Mutation testing runs project tests and is an explicit, optional operation. UML generation is static and offline. See the [CDMU quick guide](https://github.com/Yatseck-k/ub-cdmu-php/blob/main/docs/GUIDE.md) and [reference](https://github.com/Yatseck-k/ub-cdmu-php/blob/main/docs/README.md) for prerequisites, commands, statuses and exit codes.

## Repository structure

```text
SKILL.md      Agent instructions (the only required installation file)
README.md     English overview
README.ru.md  Russian overview
```

## Related projects

- [ub-cdmu-php](https://github.com/Yatseck-k/ub-cdmu-php) — optional PHP analysis CLI.
- [commit-rules](https://github.com/Yatseck-k/commit-rules) — standalone skill for Conventional Commits and PR descriptions.

## Attribution

Independently maintained adaptation inspired by [unclebob/ub-mode](https://github.com/unclebob/ub-mode). Original companion tools: [Crapper](https://github.com/unclebob/crapper), [Dryer](https://github.com/unclebob/dryer), [Mutator](https://github.com/unclebob/mutator), and [UML Viewer](https://github.com/unclebob/uml-viewer).
