# Миграция на Codex CLI

Этот гайд описывает, как перенести существующую инсталляцию My Brain Is Full — Crew с Claude Code, Gemini CLI или OpenCode на Codex CLI. Также объясняет, что переносится автоматически, а что требует ручного внимания.

> **Если вы делаете чистую установку** (не миграцию), смотрите вместо этого [docs/codex-cli.md](codex-cli.md).

---

## Когда переустанавливать, а когда обновлять

| Сценарий | Рекомендуемое действие |
|----------|------------------------|
| У вас уже есть хранилище под Claude Code / Gemini CLI / OpenCode, и вы хотите добавить Codex CLI рядом | Запустите `bash scripts/launchme.sh --platform codex-cli` в том же хранилище — несколько платформ могут сосуществовать |
| Вы хотите перейти исключительно на Codex CLI | Запустите `launchme.sh --platform codex-cli`; файлы других платформ остаются, но становятся неактивны |
| Раскладка Codex сломана или отсутствуют файлы | Запустите `launchme.sh --platform codex-cli` снова — он идемпотентен и его безопасно перезапускать |
| Вы подтянули новые изменения репо и хотите обновить Codex | Запустите `bash scripts/updateme.sh --platform codex-cli` |

Удалять папки других платформ не нужно. Codex CLI читает только `.codex/` и `.agents/skills/`; он игнорирует `.claude/`, `.gemini/` и `.opencode/`.

---

## Маппинг путей по платформам

При переходе на Codex CLI файлы проекта переезжают на новые пути. Используйте таблицу, чтобы найти ваши текущие файлы и понять, где живёт их эквивалент в Codex.

| Исходная платформа | Диспетчер | Агенты | Навыки | MCP или конфиг | Цель в Codex |
|--------------------|-----------|--------|--------|----------------|--------------|
| Claude Code | `CLAUDE.md` | `.claude/agents/*.md` | `.claude/skills/` | `.mcp.json` | `AGENTS.md` / `.codex/agents/*.toml` / `.agents/skills/` / `.codex/config.toml` |
| Gemini CLI | `GEMINI.md` | `.gemini/agents/*.md` | `.gemini/skills/` | (нет) | `AGENTS.md` / `.codex/agents/*.toml` / `.agents/skills/` / `.codex/config.toml` |
| OpenCode | `AGENTS.md` | `.opencode/agents/*.md` | `.opencode/skills/` | `opencode.json` | `AGENTS.md` / `.codex/agents/*.toml` / `.agents/skills/` / `.codex/config.toml` |

После запуска `launchme.sh --platform codex-cli` файлы Codex устанавливаются автоматически. Копировать старые файлы платформы вручную не нужно.

---

## Переход с Claude Code

1. Подтяните последние изменения репо:
   ```bash
   cd /path/to/your-vault/My-Brain-Is-Full-Crew
   git pull
   ```

2. Запустите установщик Codex:
   ```bash
   bash scripts/launchme.sh --platform codex-cli
   ```

3. Установщик создаст:
   - `.codex/agents/` — все 8 основных агентов в формате TOML
   - `.agents/skills/` — все 14 навыков как текстовые инструкции
   - `.codex/config.toml` — MCP-серверы (переведённые из `mcp/servers.yaml`)
   - `AGENTS.md` — диспетчер с шапкой маршрутизации Codex

4. Ваша существующая папка `.claude/` и файл `CLAUDE.md` остаются нетронутыми.

5. Конфигурация MCP: Claude Code использует `.mcp.json`. Codex CLI использует `.codex/config.toml`. Если вы добавляли свои MCP-серверы в `.mcp.json` вручную, придётся добавить их и в `.codex/config.toml`. Смотрите формат TOML-таблицы `[mcp_servers.*]`.

6. Пользовательские агенты: пользовательские агенты Claude Code лежат в `.claude/agents/`. Пользовательские агенты Codex CLI должны быть в формате `.toml` в `.codex/agents/`. Агенты, созданные через навык `/create-agent`, автоматически не мигрируют — смотрите [Пользовательские агенты и что не мигрирует автоматически](#пользовательские-агенты-и-что-не-мигрирует-автоматически).

---

## Переход с Gemini CLI

1. Подтяните последние изменения репо:
   ```bash
   cd /path/to/your-vault/My-Brain-Is-Full-Crew
   git pull
   ```

2. Запустите установщик Codex:
   ```bash
   bash scripts/launchme.sh --platform codex-cli
   ```

3. Установщик создаёт полную раскладку Codex (как выше).

4. Ваша существующая папка `.gemini/` и файл `GEMINI.md` остаются нетронутыми.

5. Конфигурация MCP: Gemini CLI не использует `.mcp.json`. Если MCP-серверы настроены где-то ещё, добавьте их в `.codex/config.toml` вручную.

6. Пользовательские агенты: пользовательские агенты Gemini CLI лежат в `.gemini/agents/`. Это Markdown-файлы. Для Codex CLI пользовательские агенты должны быть TOML-файлами в `.codex/agents/`. Смотрите [Пользовательские агенты и что не мигрирует автоматически](#пользовательские-агенты-и-что-не-мигрирует-автоматически).

---

## Переход с OpenCode

1. Подтяните последние изменения репо:
   ```bash
   cd /path/to/your-vault/My-Brain-Is-Full-Crew
   git pull
   ```

2. Запустите установщик Codex:
   ```bash
   bash scripts/launchme.sh --platform codex-cli
   ```

3. Установщик создаёт полную раскладку Codex. Обратите внимание: и OpenCode, и Codex CLI используют `AGENTS.md` в качестве диспетчера. Установщик перезапишет `AGENTS.md` Codex-специфичной версией (со шапкой root-context routing). Если вы используете обе платформы из одного хранилища — учтите, что `AGENTS.md` у них общий.

4. Ваша существующая папка `.opencode/` остаётся нетронутой.

5. Конфигурация MCP: OpenCode использует `opencode.json`. Codex CLI использует `.codex/config.toml`. Если вы добавляли свои MCP-серверы в `opencode.json` — добавьте их в `.codex/config.toml` вручную.

6. Пользовательские агенты: пользовательские агенты OpenCode лежат в `.opencode/agents/` как Markdown-файлы. Пользовательские агенты Codex CLI должны быть TOML-файлами в `.codex/agents/`. Смотрите [Пользовательские агенты и что не мигрирует автоматически](#пользовательские-агенты-и-что-не-мигрирует-автоматически).

---

## Пользовательские агенты и что не мигрирует автоматически

Когда вы запускаете установщик, 8 основных агентов команды автоматически переводятся в формат Codex TOML. Однако **пользовательские агенты, созданные через `/create-agent`**, автоматически не мигрируют, потому что:

- Они живут в директории агентов вашей платформы (`.claude/agents/`, `.gemini/agents/` и т.д.)
- Это Markdown-файлы; Codex требует TOML
- Установщик никогда не перезаписывает и не удаляет файлы в директории агентов, которые он не создавал сам

### Как мигрировать пользовательского агента вручную

1. Найдите файл вашего пользовательского агента (например, `.claude/agents/budget-tracker.md`)
2. Откройте Codex CLI в хранилище и запустите `/create-agent`
3. Опишите назначение агента — Архитектор проведёт вас через создание нового `.toml`-файла в `.codex/agents/`
4. Альтернативно, создайте TOML-файл вручную, используя один из сгенерированных основных агентов как шаблон (например, `.codex/agents/scribe.toml`)

### Как выглядит формат TOML

```toml
[agent]
name = "budget-tracker"
description = "Отслеживает заметки о тратах и предупреждает, когда близко к месячному лимиту"
model = "o4-mini"

[agent.prompt]
content = """
Ты — агент Budget Tracker для системы My Brain Is Full — Crew.
... (инструкции вашего агента здесь)
"""
```

---

## Проверка после миграции

После запуска установщика проверьте раскладку Codex этими командами:

### Убедитесь, что файлы поставлены корректно

```bash
ls <vault>/.codex/agents/       # Должен показать *.toml-файлы для всех 8 агентов
ls <vault>/.agents/skills/      # Должен показать подпапки для всех 14 навыков
ls <vault>/.codex/config.toml   # Должен существовать с таблицами [mcp_servers.*]
ls <vault>/AGENTS.md            # Должен существовать с шапкой маршрутизации Codex
```

### Запустите неинтерактивный дымовой тест discovery

```bash
codex exec -C <vault> "List the project custom agents under .codex/agents, the repo skills under .agents/skills, and the dispatcher file used in this workspace."
```

Ожидаемо: ответ ссылается на `AGENTS.md`, `.codex/agents` и `.agents/skills`.

### Проверьте видимость MCP

```bash
codex -C <vault> mcp list
```

Ожидаемо: перечисляет MCP-серверы из `.codex/config.toml`.

### Прогоните полную дымовую матрицу рантайма

Смотрите [docs/codex-cli.md — Дымовая матрица рантайма](codex-cli.md#дымовая-матрица-рантайма) для полного списка проверок агентов, навыков, цепочек и MCP.
