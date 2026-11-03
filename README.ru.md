# ub-mode-php

**Скилл для AI-агентов: дисциплинированная разработка PHP-кода с опорой на тесты.**

[English](README.md) · [Инструкции скилла](SKILL.md) · [Оригинальный ub-mode](https://github.com/unclebob/ub-mode)

![PHP](https://img.shields.io/badge/Focus-PHP-777BB4?logo=php&logoColor=white) ![AI agents](https://img.shields.io/badge/AI-agent%20skill-374151)

## Назначение

`ub-mode-php` адаптирует трёхэтапный подход [Uncle Bob's ub-mode](https://github.com/unclebob/ub-mode) для PHP-проектов и AI-агентов. **Скилл работает без обязательных зависимостей и дополнительных анализаторов.** Устанавливается под именем `ub-mode`.

| Этап | Задачи |
| --- | --- |
| **First Make It Work** | Небольшие изменения, TDD, проверка поведения и границ |
| **Then Make It Right** | Поиск причин проблем, ревью, сложность, дублирование, мутации и рефакторинг |
| **Practice** | Рабочий журнал, контекст, очистка и дисциплина коммитов |

## Установка

Для установки нужен только [SKILL.md](SKILL.md). Например, для Codex:

```bash
mkdir -p ~/.codex/skills/ub-mode
cp SKILL.md ~/.codex/skills/ub-mode/SKILL.md
```

Для других агентов с поддержкой `SKILL.md` используйте их каталог скиллов. Вызывайте установленный скилл поддерживаемым агентом способом, например `$ub-mode`.

## Необязательный анализатор PHP

Отдельный Composer-пакет [ub-cdmu-php](https://github.com/Yatseck-k/ub-cdmu-php) добавляет метрики CRAP, поиск дублирования, мутационное тестирование и UML. **Анализатор необязателен и не устанавливается автоматически.** Для CLI сейчас требуется **PHP 8.4.27+ (до 8.5) или PHP 8.5.12+ (до 9.0)**. Сам скилл этих требований не имеет.

Установка анализатора из GitHub в совместимый PHP-проект:

```bash
composer config repositories.ub-cdmu-php vcs https://github.com/Yatseck-k/ub-cdmu-php
composer require --dev yatseck-k/ub-cdmu-php:dev-main
```

После запуска тестов проекта агент может использовать:

```bash
vendor/bin/ub-php doctor --format json
vendor/bin/ub-php check --changed --format json
vendor/bin/ub-php dry --changed --threshold 0.8 --format json
```

`--changed` требует Git; отсутствие изменённых файлов не означает проверку всего проекта. Агент учитывает коды завершения и поля структурированных отчётов. Нет покрытия — результат **неизвестен**, а не успешен. Найденные дубликаты требуют ревью.

Мутационное тестирование отдельно запускает тесты проекта; UML работает статически и автономно. Подробнее: [быстрый старт CDMU](https://github.com/Yatseck-k/ub-cdmu-php/blob/main/docs/GUIDE.ru.md) и [справочник](https://github.com/Yatseck-k/ub-cdmu-php/blob/main/docs/README.ru.md).

## Структура

```text
SKILL.md      Инструкции для агента — единственный необходимый файл
README.md     Английское описание
README.ru.md  Русское описание
```

## Связанные проекты

- [ub-cdmu-php](https://github.com/Yatseck-k/ub-cdmu-php) — необязательный анализатор PHP.
- [commit-rules](https://github.com/Yatseck-k/commit-rules) — отдельный скилл Conventional Commits.

## Происхождение

Независимая адаптация на основе [unclebob/ub-mode](https://github.com/unclebob/ub-mode). Оригинальные инструменты: [Crapper](https://github.com/unclebob/crapper), [Dryer](https://github.com/unclebob/dryer), [Mutator](https://github.com/unclebob/mutator), [UML Viewer](https://github.com/unclebob/uml-viewer).
