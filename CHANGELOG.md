# Changelog

Все заметные изменения в этом форке `My-Brain-Is-Full-Crew` фиксируются здесь.

Формат основан на [Keep a Changelog](https://keepachangelog.com/ru/1.1.0/),
проект придерживается [семантического версионирования](https://semver.org/lang/ru/).

Каноничный апстрим: [gnekt/My-Brain-Is-Full-Crew](https://github.com/gnekt/My-Brain-Is-Full-Crew).

---

## [2026.05.12] — 2026-05-12

Слияние upstream-коммита `340be6e` (vault path tokenization) с локальной веткой,
содержащей premortem-функционал и русские переводы документации.

### CI / Release

- Добавлен `.github/workflows/release.yml` — автоматический GitHub-релиз
  при изменении `CHANGELOG.md` или ручном `workflow_dispatch`. Парсит первый
  раздел `## [...] — YYYY-MM-DD`, создаёт тег `v{date}` (или из заголовка),
  собирает install-бандл `agents/ + references/ + scripts/ + adapters/ +
  mcp/ + docs/` в tar.gz и прикрепляет к релизу.
- Permissions: `contents: write`, `packages: write` (на случай будущей
  публикации npm-пакета в GitHub Packages).

### Добавлено (из upstream)

- **Токенизация путей хранилища (vault tokenization).** Crew теперь умеет
  работать с любой структурой Obsidian-хранилища — больше не требуется
  переименовывать папки под `00-Inbox/`, `01-Projects/` и т.д.
- **`Meta/vault-map.md`** — новый конфиг-файл, который привязывает 11
  логических ролей (`inbox`, `projects`, `areas`, `resources`, `archive`,
  `people`, `meetings`, `daily`, `templates`, `meta`, `moc`) к именам ваших
  реальных папок. Создаётся Архитектором во время онбординга, либо
  редактируется вручную в любое время — изменения вступают в силу сразу.
- **Vault-role токены** (`{{inbox}}`, `{{projects}}`, …) в промптах всех
  агентов и навыков. На рантайме токены резолвятся в реальные пути из
  `vault-map.md`. Только эти 11 токенов подставляются — остальные
  `{{...}}` (например, `{{date}}`, `{{Name}}`) остаются шаблонными
  плейсхолдерами.
- **`docs/vault-mapping.md`** — новый пользовательский гайд по токенизации
  (переведён на русский в этом коммите).
- **Адаптация к существующим хранилищам.** Архитектор сканирует вашу
  текущую структуру и не пытается ничего сломать — если у вас уже есть
  `Inbox/` вместо `00-Inbox/`, он просто запишет это в `vault-map.md`.
- **Бэк-компат.** Если `vault-map.md` отсутствует, агенты падают на
  встроенные дефолты — существующие пользователи никак не страдают.

### Изменено (из upstream)

- Обновлён 41 файл: все ядровые агенты (`agents/architect.md`,
  `connector.md`, `librarian.md`, `postman.md`, `scribe.md`, `seeker.md`,
  `sorter.md`, `transcriber.md`), все 14 навыков (`skills/*/SKILL.md`),
  диспетчер (`DISPATCHER.md`), общие референсы (`references/agents.md`,
  `references/agents-registry.md`), пользовательская документация в `docs/`,
  скрипты (`scripts/lib.sh`), `README.md`, `CONTRIBUTING.md`.
- `DISPATCHER.md` получил блок про vault-map и правила резолвинга
  токенов (раздел «Vault path resolution»).
- `scripts/lib.sh` обзавёлся хелперами для подстановки токенов на этапе
  адаптеров.

### Сохранено локально (наше)

- **Premortem-протокол** (Klein / Kahneman) — сквозная возможность
  для всех агентов. Файл `references/premortem-protocol.md` (281 строка)
  и блоки про premortem в каждом ядровом агенте сохранены. Upstream
  удалил этот файл, мы оставили (стратегия `ours`).
- **Русский перевод документации** (`docs/DISCLAIMERS.md`,
  `docs/examples.md`, `docs/getting-started.md`, `docs/mobile-access.md`,
  все `docs/agents/*.md`, `DISPATCHER.md`-русские блоки, `README.md`).
  Auto-merge git'а корректно сохранил русские строки везде, где они были
  длиннее upstream-английских и не пересекались по контексту.
- **Адаптер Codex CLI** в дереве каталогов README.md (строки про
  `adapters/codex-cli/`).
- **Персональные правки README** (нет Discord-бэйджа, нет вводки
  про PhD/студенчество).

### Конфликты merge и как разрешены

| Файл | Тип конфликта | Решение |
|------|---------------|---------|
| `README.md` | 4 блока: русский HEAD против английского upstream + новые куски про vault | Сохранён русский текст, новые upstream-добавления (vault-mapping ссылки, vault-map.md в дереве, фраза про адаптацию) переведены и интегрированы |
| `references/premortem-protocol.md` | upstream удалил, у нас добавлен | Файл сохранён (стратегия `ours`) |
| `agents/*.md` (8 файлов) | auto-merge | Без конфликтов — git склеил наш premortem-блок с upstream-токенами |

### Переведено

- `docs/vault-mapping.md` — новый upstream-файл, переведён с английского
  на русский для соответствия паттерну `docs/* — для людей, по-русски`.

---

## [0.3.0] — 2026-05-12 (локальный)

### Добавлено

- **Premortem (Klein / Kahneman)** как сквозная возможность для всех
  агентов. Полная спецификация в `references/premortem-protocol.md`
  (триггеры, маппинг 11 категорий на папки, шаблон файла с фронтматтером и
  7 секциями, таблица маршрутизации, частые ошибки).
- Premortem-блоки в каждом ядровом агенте:
  - `scribe` — создаёт скелеты premortem-заметок из шаблона
  - `seeker` — ищет существующие premortem'ы, отвечает по содержимому
  - `architect` — поддерживает структуру `Premortem/`-папки и шаблон
  - `librarian` — health-чеки (stale-даты, недостающие секции, Kanban-синк)
  - `postman`, `transcriber`, `sorter`, `connector` — awareness-блоки
    для кросс-ссылок
- Новая секция «Premortem (cross-cutting capability)» в `DISPATCHER.md`
  между `LIBRARIAN` и `CUSTOM AGENTS`, с таблицей маршрутизации 13
  доменных триггеров (финансы, здоровье, психология, еда, расходы, карьера…)
  на специалистов.
- Premortem-колонка в `references/agents-registry.md`
  (full / awareness / —) и секция cross-cutting capability в
  `references/agents.md`.

Коммит: `0d062d0`.

---

## [0.2.0] — 2026-04-24

### Добавлено (синк с upstream)

- Мульти-платформенная архитектура адаптеров (Claude Code, Gemini CLI,
  OpenCode, Codex CLI). Один источник → 4 нативных формата.
- Навык `/contact-sync` для интеграции с Apple Contacts.
- Папка `orchestra/` со скриптами для permission-free операций агентов.
- Тестовый каркас в `tests/`.
- `DISPATCHER.md` как явный документ-маршрутизатор.

### Сохранено локально

- Русский перевод `README.md` синхронизирован с upstream-контентом.
- Русский перевод `docs/{DISCLAIMERS,examples,getting-started,mobile-access}.md`
  оставлен (стратегия `ours`).
- Личные правки README (без Discord-бейджа, без блока PhD/студента).

Коммит: `ac67191`.

---

## [0.1.0] — изначальный форк

Стартовое состояние форка `Fighter90/My-Brain-Is-Full-Crew` от
`gnekt/My-Brain-Is-Full-Crew`. Включает базовых 8 ядровых агентов
(architect, connector, librarian, postman, scribe, seeker, sorter,
transcriber) и набор скиллов для управления Obsidian-хранилищем.
