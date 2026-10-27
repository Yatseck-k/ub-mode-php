# ub-mode-php

[Русская версия](README.ru.md)

An independent agent skill for disciplined code changes. It works with the target project's tests and code review alone. Optional analyzers add reproducible measurements when installed. The skill is separate from the `ub-cdmu-php` tools repository and contains no Python, PHP, Composer, or Docker dependency. The skill is invoked as `ub-mode`.

The skill follows the upstream sequence: **First Make it Work** (small changes, TDD, design and boundaries), **Then Make it Right** (root causes, behavior tests, complexity, duplication, mutation and refactoring), and **Practice** (work log, context, cleanup and commits). Each principle remains actionable without an analyzer.

## Install locally

From this checkout, copy the skill to the directory used by your agent. For Codex:

```sh
mkdir -p ~/.codex/skills/ub-mode
cp SKILL.md ~/.codex/skills/ub-mode/SKILL.md
```

For Claude Code or Cursor, copy it as `ub-mode/SKILL.md` under that agent's skills directory. Invoke `$ub-mode` or `/ub-mode` according to the agent.

## How it uses tools

The agent first runs the project's configured tests and reviews changed code. Without optional analyzers, it continues with tested refactoring and reports complexity or duplication observations without invented scores.

For PHP projects with the separate `ub-cdmu-php` CLI installed, run from the project root:

```sh
ub-php check --changed --format json
ub-php dry --changed --threshold 0.8
```

The CLI currently provides CRAP and Dryer checks. Mutation testing and UML visualization depend on separate tools if a project provides them; the skill does not require either. Missing Clover coverage stays unknown. See the CLI's own documentation for installation and exit codes.

## Provenance

This skill follows the three-stage structure of [unclebob/ub-mode](https://github.com/unclebob/ub-mode). Its instructions were adapted for optional tools and PHP project workflows; the original skill and its companion [Crapper](https://github.com/unclebob/crapper), [Dryer](https://github.com/unclebob/dryer), [Mutator](https://github.com/unclebob/mutator), and [UML Viewer](https://github.com/unclebob/uml-viewer) are independent upstream projects.
