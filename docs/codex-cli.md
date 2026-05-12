# Гайд по Codex CLI

Этот гайд покрывает всё необходимое для установки, обновления и запуска My Brain Is Full — Crew на [Codex CLI](https://openai.com/codex) (`@openai/codex`).

> **Заметка для Windows:** поддержка Windows в Codex CLI экспериментальная. Если вы на Windows, настоятельно рекомендуется запускать его внутри WSL (Windows Subsystem for Linux).

---

## Команды установки и обновления

### Первичная установка

```bash
# Глобально ставим Codex CLI
npm i -g @openai/codex@latest

# Клонируем репо внутрь хранилища и устанавливаем Crew
cd /path/to/your-vault
git clone https://github.com/gnekt/My-Brain-Is-Full-Crew.git
cd My-Brain-Is-Full-Crew
bash scripts/launchme.sh --platform codex-cli
```

Установщик принимает опциональный флаг `--target`, если хранилище лежит в нестандартном месте:

```bash
bash scripts/launchme.sh --platform codex-cli --target /path/to/your-vault
```

### Обновление после git pull

```bash
cd /path/to/your-vault/My-Brain-Is-Full-Crew
git pull
bash scripts/updateme.sh --platform codex-cli
```

Обновляющий скрипт автоматически определяет Codex CLI по наличию `.codex/agents` в хранилище. Если установлено несколько платформ — передайте `--platform codex-cli` явно.

---

## Что куда устанавливается

После запуска `launchme.sh --platform codex-cli` хранилище будет выглядеть так:

```
your-vault/
├── .codex/
│   ├── agents/          ← 8 основных агентов команды (формат .toml)
│   ├── references/      ← общие документы, которые читают агенты
│   └── config.toml      ← MCP-серверы + профили + sandbox-политика
├── .agents/
│   └── skills/          ← 14 специализированных навыков (текстовые инструкции)
├── Meta/
│   └── scripts/         ← скрипты orchestra (команды агентов без prompt)
└── AGENTS.md            ← диспетчер (project instructions для Codex)
```

Ключевые отличия от других платформ:

| Путь | Назначение |
|------|------------|
| `.codex/agents/*.toml` | Определения пользовательских агентов (нативный формат Codex) |
| `.agents/skills/` | Навыки на уровне репо (общий путь обнаружения) |
| `.codex/config.toml` | MCP-серверы, политика подтверждений, sandbox-режим, профили моделей |
| `AGENTS.md` | Диспетчер — Codex читает его как основной файл project instructions |

---

## Отличия архитектуры от Claude Code, Gemini CLI и OpenCode

### Диспетчер

Все платформы используют файл-диспетчер, но имя и формат отличаются:

| Платформа | Файл-диспетчер |
|-----------|----------------|
| Claude Code | `CLAUDE.md` |
| Gemini CLI | `GEMINI.md` |
| OpenCode | `AGENTS.md` |
| Codex CLI | `AGENTS.md` (с заголовком root-context routing) |

Codex CLI делит имя `AGENTS.md` с OpenCode, но добавляет в начало шапку маршрутизации, которая решает оркестровку в условиях ограничения `agents.max_depth = 1` (см. ниже).

### Формат агентов

Claude Code, Gemini CLI и OpenCode используют Markdown (`.md`) для агентов. Codex CLI использует TOML:

```
.claude/agents/architect.md        ← Claude Code
.gemini/agents/architect.md        ← Gemini CLI
.opencode/agents/architect.md      ← OpenCode
.codex/agents/architect.toml       ← Codex CLI
```

### Расположение навыков

В Codex навыки ставятся в `.agents/skills/` (а не в `.codex/skills/`). Codex CLI ищет навыки по этому общему пути.

### Цепочки агентов (ограничение max_depth)

Codex CLI принудительно устанавливает `agents.max_depth = 1`. Это значит, что дочерние агенты могут уходить только на один уровень вниз. My Brain Is Full — Crew решает это через оркестровку из root-контекста:

- Диспетчер встраивает инструкции оркестровки в root-контекст (не в дочернего)
- Дочерние агенты (`spawn_agent`) делают одну ограниченную задачу и возвращаются в root
- Любой следующий шаг решается из root-контекста, а не вложенным дочерним

### Различия в именах инструментов

В Codex CLI нет инструментов `AskUserQuestion` и `request_user_input`. Эквивалентные паттерны:

| Концепт-источник | Эквивалент в Codex CLI |
|---|---|
| `AskUserQuestion` | Задайте прямой вопрос в треде чата и дождитесь ответа |
| `request_user_input` | То же — используйте root-беседу для уточняющих вопросов |
| `Skill tool` | Следуйте инструкциям навыка прямо в root-контексте |
| `Agent tool` | Используйте `spawn_agent` для ограниченной дочерней задачи; оркестровка возвращается в root |
| `max chain depth 3` | `agents.max_depth = 1` с оркестровкой только в root |
| `.mcp.json` | `.codex/config.toml` |

### Конфигурация MCP

Claude Code использует `.mcp.json`. Codex CLI использует `.codex/config.toml`. Все настройки MCP-серверов, политики подтверждений, режима sandbox и профилей моделей лежат в этом TOML-конфиге. CLI и расширение Codex IDE используют один и тот же файл.

---

## Дымовая матрица рантайма

Используйте эту таблицу, чтобы убедиться, что Crew корректно работает в реальном Codex-хранилище после установки или обновления. Прогоните каждую строку и сравните результат с ожидаемым.

| Поверхность | Имя | Запрос или команда | Ожидаемый результат |
|-------------|-----|--------------------|---------------------|
| Агент | Architect | `@Architect Set up my vault structure` | Architect начинает диалог онбординга или подтверждает, что хранилище уже настроено |
| Агент | Scribe | `@Scribe Save this note: quick test` | Scribe создаёт заметку в 00-Inbox с корректным фронтматтером |
| Агент | Sorter | `@Sorter Triage my inbox` | Sorter просматривает заметки в инбоксе и раскладывает их, или сообщает, что инбокс пуст |
| Агент | Seeker | `@Seeker What do I know about this project?` | Seeker ищет по хранилищу и возвращает результаты со ссылками на источники |
| Агент | Connector | `@Connector Find connections in my recent notes` | Connector анализирует граф хранилища и предлагает wikilinks |
| Агент | Librarian | `@Librarian Run a vault health check` | Librarian сканирует на битые ссылки, дубли и осиротевшие заметки |
| Агент | Transcriber | `@Transcriber Process this transcript: [paste text]` | Transcriber генерирует структурированные заметки встречи |
| Агент | Postman | `@Postman Check my email` | Postman сканирует Gmail (или Hey) и сохраняет письма, требующие действий, или сообщает об отсутствии интеграции |
| Навык | onboarding | `/onboarding` | Architect запускает полный диалог онбординга |
| Навык | create-agent | `/create-agent` | Architect проводит через дизайн нового пользовательского агента |
| Навык | manage-agent | `/manage-agent` | Architect перечисляет, редактирует или удаляет пользовательских агентов |
| Навык | defrag | `/defrag` | Architect запускает 5-фазную дефрагментацию хранилища |
| Навык | email-triage | `/email-triage` | Postman сканирует и приоритизирует непрочитанные письма |
| Навык | meeting-prep | `/meeting-prep` | Postman собирает подробный бриф к встрече |
| Навык | weekly-agenda | `/weekly-agenda` | Postman формирует обзор недели по дням |
| Навык | deadline-radar | `/deadline-radar` | Postman формирует единый таймлайн дедлайнов |
| Навык | transcribe | `/transcribe` | Transcriber превращает запись или транскрипт в структурированные заметки |
| Навык | vault-audit | `/vault-audit` | Librarian запускает полный 7-фазный аудит хранилища |
| Навык | deep-clean | `/deep-clean` | Librarian выполняет расширенную чистку хранилища |
| Навык | tag-garden | `/tag-garden` | Librarian анализирует и приводит теги в порядок |
| Навык | inbox-triage | `/inbox-triage` | Sorter обрабатывает и распределяет все заметки из инбокса |
| Навык | contact-sync | `/contact-sync` | Postman синхронизирует контакты с Apple Contacts |
| Цепочка | ограниченная цепочка дочернего агента | `@Sorter Triage my inbox` (с заметками, упоминающими новый проект) | Sorter раскладывает заметки, затем диспетчер вызывает Architect для создания папки нового проекта; дочерний возвращается в root перед стартом Architect |
| MCP | видимость MCP | `codex -C <vault> mcp list` | Перечисляет MCP-серверы, настроенные в `.codex/config.toml`, либо показывает состояние auth/setup для каждого |

### Запуск неинтерактивного дымового теста discovery

```bash
codex exec -C <vault> "List the project custom agents under .codex/agents, the repo skills under .agents/skills, and the dispatcher file used in this workspace."
```

Ожидаемый вывод должен ссылаться на:
- `AGENTS.md` (диспетчер)
- путь `.codex/agents` (пользовательские агенты)
- путь `.agents/skills` (скилы репо)

### Запуск дымового теста видимости MCP

```bash
codex -C <vault> mcp list
```

Ожидаемо: перечисляет MCP-серверы из `.codex/config.toml` (например, `Gmail`, `Calendar`) или показывает их состояние auth/setup.

---

## Поиск неисправностей

### Агенты не обнаруживаются

- Убедитесь, что `.codex/agents/` существует в корне хранилища и содержит файлы `.toml`.
- Открывайте Codex CLI из папки хранилища: `codex -C /path/to/your-vault`
- Проверьте, что `AGENTS.md` лежит в корне хранилища (а не внутри подпапки репо).

### Навыки недоступны

- Убедитесь, что `.agents/skills/` существует в корне хранилища и содержит подпапки.
- Навыки должны быть на уровне корня хранилища: `<vault>/.agents/skills/<skill-name>/`

### Цепочка дочернего агента не возвращается в root

- Это ограничение Codex `agents.max_depth = 1`. Дочерние агенты могут уходить только на один уровень.
- Диспетчер использует оркестровку из root-контекста, чтобы работать в рамках этого ограничения.
- Если задача требует более глубокой вложенности — расплющите её: завершите первый ограниченный шаг в дочернем, а следующий шаг сделайте в root-контексте.

### MCP-сервер не подключается

- Конфигурация MCP лежит в `.codex/config.toml` (не в `.mcp.json`).
- Проверьте `codex -C <vault> mcp list`, чтобы увидеть текущий статус серверов.
- Для настройки Gmail/Calendar см. `docs/gws-setup-guide.md`.
- Для Apple Contacts проверьте запись `apple-contacts` в `.codex/config.toml`.

### Ошибки Codex про подтверждения

- Подтверждения дочерних агентов всплывают в треде дочернего. Разрешите или отклоните там, затем продолжите оркестровку из root-контекста после возврата дочернего.
- Если задача требует более глубокой рекурсии — перестаньте спаунить детей и расплющите следующий шаг в root-контекст, либо разбейте работу на отдельные ограниченные дочерние задачи.

### Windows-пользователи

Поддержка Windows в Codex CLI экспериментальная. Используйте WSL (Windows Subsystem for Linux) для самого надёжного опыта. Из WSL следуйте стандартному Linux-пути установки выше.

### Переустановка против обновления

- **Переустановка** (`launchme.sh`): когда настраиваете новое хранилище или восстанавливаетесь из сломанного состояния.
- **Обновление** (`updateme.sh`): после `git pull` — пушит новых агентов, навыки и референсы в существующее хранилище. Пользовательские агенты никогда не перезаписываются.

Для миграции с другой платформы см. [docs/codex-migration.md](codex-migration.md).
