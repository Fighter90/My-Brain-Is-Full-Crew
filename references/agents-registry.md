# Agent Registry

This file is the **single source of truth** for all active agents in the crew. The dispatcher (`CLAUDE.md`) and all agents reference this file for routing decisions and inter-agent coordination.

The registry is designed to grow: custom agents (see Issue #12) are added as new rows following the same schema.

---

## Registry

> **Premortem column** (added 2026-05-12): `full` = агент умеет создавать и разворачивать premortem-файлы в своей зоне ответственности; `awareness` = агент знает о системе premortem и может предложить запустить её через релевантный домен-агент; `—` = не применимо. Подробности: `.claude/references/premortem-protocol.md`.

| Name | Role | Capabilities | Input | Output | Premortem | Status |
|------|------|-------------|-------|--------|-----------|--------|
| architect | Vault Structure & Governance | Create/modify folders, templates, MOCs, tag taxonomy, naming conventions. Full Bash access. Runs onboarding. | Vault setup, new areas/projects, structural changes, defrag, onboarding | Folders created, templates defined, structure updated, MOCs generated | full | active |
| scribe | Text Capture & Refinement | Create notes in `00-Inbox/`, format raw text, handle voice-to-note, brainstorm, quotes, reading notes | Raw text, ideas, thoughts, voice input, quotes, brainstorm requests | Structured notes in `00-Inbox/` with frontmatter, tags, suggested connections | full | active |
| sorter | Inbox Triage & Filing | Move notes from inbox to correct locations, update MOCs, batch processing | Inbox triage, filing requests, note organization | Notes moved to correct folders, MOCs updated, triage reports | awareness | active |
| seeker | Search & Intelligence | Full-text search, metadata queries, relationship navigation, answer synthesis. Read-only by default. | Search queries, "find X", "where did I put", factual questions about vault content | Search results with citations, synthesized answers, knowledge gap reports | full | active |
| connector | Knowledge Graph & Link Analysis | Add/edit wikilinks, analyze graph structure, discover connections, bridge notes | Link analysis, "find connections", graph health, serendipity requests | New wikilinks added, graph health score, connection maps, bridge notes | awareness | active |
| librarian | Vault Health & Quality Assurance | Detect/merge duplicates, fix broken links, audit frontmatter, growth analytics. Full Bash access. | Maintenance, audit, cleanup, health check, duplicate detection | Health reports, fixed links, merged duplicates, consistency reports | full | active |
| transcriber | Audio & Meeting Intelligence | Process transcriptions into structured notes, extract action items, speaker detection | Audio recordings, transcriptions, meeting notes, lecture/podcast processing | Structured meeting/lecture notes in `00-Inbox/` with action items, decisions, topics | awareness | active |
| postman | Email & Calendar Intelligence | Read/archive/delete email (Gmail via `gws`, Hey.com via `hey`), search emails, read/create/update calendar events, draft and send replies. Uses Google Workspace CLI (`gws`) and/or Hey CLI (`hey`) via Bash, with MCP as read-only fallback. | Email triage, calendar queries, deadline tracking, meeting prep, VIP filtering | Email summaries saved as notes in `00-Inbox/`, calendar events created, deadline reports | awareness | active |
<!-- MBIFC:CUSTOM_AGENTS_START -->
| architect | Vault Structure & Governance | Create/modify folders, templates, MOCs, tag taxonomy, naming conventions. Full Bash access. Runs onboarding. | Vault setup, new areas/projects, structural changes, defrag, onboarding | Folders created, templates defined, structure updated, MOCs generated | full | active |
| scribe | Text Capture & Refinement | Create notes in `00-Inbox/`, format raw text, handle voice-to-note, brainstorm, quotes, reading notes | Raw text, ideas, thoughts, voice input, quotes, brainstorm requests | Structured notes in `00-Inbox/` with frontmatter, tags, suggested connections | full | active |
| sorter | Inbox Triage & Filing | Move notes from inbox to correct locations, update MOCs, batch processing | Inbox triage, filing requests, note organization | Notes moved to correct folders, MOCs updated, triage reports | awareness | active |
| seeker | Search & Intelligence | Full-text search, metadata queries, relationship navigation, answer synthesis. Read-only by default. | Search queries, "find X", "where did I put", factual questions about vault content | Search results with citations, synthesized answers, knowledge gap reports | full | active |
| connector | Knowledge Graph & Link Analysis | Add/edit wikilinks, analyze graph structure, discover connections, bridge notes | Link analysis, "find connections", graph health, serendipity requests | New wikilinks added, graph health score, connection maps, bridge notes | awareness | active |
| librarian | Vault Health & Quality Assurance | Detect/merge duplicates, fix broken links, audit frontmatter, growth analytics. Full Bash access. | Maintenance, audit, cleanup, health check, duplicate detection | Health reports, fixed links, merged duplicates, consistency reports | full | active |
| transcriber | Audio & Meeting Intelligence | Process transcriptions into structured notes, extract action items, speaker detection | Audio recordings, transcriptions, meeting notes, lecture/podcast processing | Structured meeting/lecture notes in `00-Inbox/` with action items, decisions, topics | awareness | active |
| postman | Email & Calendar Intelligence | Read Gmail, search emails, read/create calendar events, draft replies. Uses MCP connectors. | Email triage, calendar queries, deadline tracking, meeting prep, VIP filtering | Email summaries saved as notes in `00-Inbox/`, calendar events created, deadline reports | awareness | active |
| food-diary | Дневник питания | Записывает приёмы пищи с расчётом КБЖУ, анализирует питание, даёт рекомендации по рациону | "я поел", "записать питание", "анализ питания", "я съел", "завтрак/обед/ужин/перекус" | Записи в дневнике питания с КБЖУ, анализ за день/неделю, рекомендации | full | active |
| expense-diary | Дневник расходов | Записывает траты по категориям, анализирует расходы, отслеживает бюджет | "я потратил", "расходы", "покупка", "заплатил", "бюджет", "куда уходят деньги" | Записи в дневнике расходов с категоризацией, анализ трат | full | active |
| daily-diary | Ежедневный дневник | Ведёт личный дневник: принимает мысли, события, рефлексию и структурирует в запись | "дневник", "запись в дневник", "мой день", "итоги дня", "рефлексия", "вечерний дневник" | Структурированные дневниковые записи по дням | full | active |
| coach | Персональный коуч | Помогает с целями, мотивацией, прокрастинацией, привычками, планированием | "коуч", "мотивация", "прокрастинирую", "застрял", "нет сил", "план действий" | Коучинг-сессии, action items, мотивационная поддержка | full | active |
| cbt-therapist | Психотерапевт (КПТ) | КПТ-техники: записи мыслей, когнитивные искажения, управление тревогой/гневом | "терапия", "тревога", "злюсь", "негативные мысли", "бессонница", "мне плохо", "КПТ" | Записи мыслей (thought records), CBT-упражнения, дневник терапии | full | active |
| home-doctor | Домашний доктор | Отслеживает здоровье, анализы, лекарства, визиты к врачам, симптомы | "здоровье", "анализы", "лекарства", "врач", "симптомы", "болит", "схема приёма" | Медицинская история, трекинг лекарств, логи симптомов | full | active |
| finance-analyst | Финансовый аналитик | Анализирует расходы/доходы, строит отчёты, выявляет тренды, бюджетирование | "финансовый анализ", "анализ расходов", "доходы", "инвестиции", "финансовый отчёт" | Финансовые отчёты, тренды, бюджетные планы | full | active |
| goals-kanban | Канбан целей | Управляет канбан-доской: перемещает цели между статусами, показывает прогресс | "канбан", "статус цели", "что в работе", "завершить цель", "доска целей" | Обновлённый канбан, статус-отчёты | full (обязательный 🟡→🟢) | active |
| goals-list | Список целей | Добавляет/обновляет цели, приоритизирует, декомпозирует на подзадачи | "новая цель", "добавить цель", "мои цели", "список целей", "цели на год" | Обновлённый список целей с приоритетами и подзадачами | full | active |
| therapy-diary | Дневник терапии | Записывает терапевтические сессии, эмоции, инсайты, домашние задания | "записать сессию терапии", "после терапевта", "записать чувства", "дневник сессий" | Записи сессий, трекинг эмоций, история терапии | full | active |
| vacancy-parser | Парсер вакансий (hh.ru + Труд.Всем + LaraJobs) | Парсит вакансии через 3 API, фильтрует по технологии/зарплате/региону, сохраняет отчёт в vault | "найди вакансии", "вакансии hh", "парсинг вакансий", "вакансии с зарплатой", "larajobs вакансии", "зарубежные вакансии", "remote вакансии", "труд всем вакансии" | Markdown-отчёт с таблицами вакансий, статистикой, ссылками для отклика в 02-Areas/Работа/Вакансии/ | awareness | active |
| problemhunt-parser | Парсер проблем ProblemHunt → стартапы | Парсит проблемы с ProblemHunt.pro, оценивает Fit Score по экспертности, создаёт полную документацию стартапов (8 файлов по 1500+ строк) | "проблем хант", "problemhunt", "найди проблемы", "стартап идеи", "какие проблемы могу решить", "новые проблемы" | Папки проектов в 01-Projects/ (8 файлов каждая), сводный отчёт в 02-Areas/Работа/Свои проекты/ | awareness | active |
| producthunt-parser | Парсер запусков Product Hunt → анализ стартапов | Парсит топ-20 запусков с Product Hunt (день/неделя), оценивает Fit Score по экспертности Сергея (PM/Go/AI/MBA ВШЭ/Variant.com), сравнивает с 9+ существующими проектами, создаёт стартап-проекты (3 файла: описание, анализ, идея аналога). Цвет акцента — оранжевый PH #da552f | "Product Hunt", "продакт хант", "producthunt", "найди стартапы", "топ продуктов", "новые запуски", "запуски Product Hunt", "что запустили на product hunt", "парсинг Product Hunt", "анализ Product Hunt", "20 топовых продуктов недели", "топ-20 ph" | Папки проектов 🚀 в 01-Projects/{slug}/ (3 файла каждая), сводный отчёт в 02-Areas/Работа/Свои проекты/{date} — ProductHunt анализ.md, state в Meta/states/producthunt-parser.md | awareness | active |
| techcrunch-parser | Парсер стартапов TechCrunch → анализ + RU-форк | Парсит топ-20 свежих статей о funding/launch/breakthrough на TechCrunch, делает глубокий fetch каждой статьи, ставит Fit Score 1-10 под экспертизу Сергея (PM/Go/AI/prompt engineering/релокация), создаёт стартап-профили + анализ + концепцию русского аналога. Цвет акцента — зелёный TechCrunch #0a9444, эмодзи 🚀💰🧪🇷🇺 | "TechCrunch", "тех-кранч", "techcrunch", "найди стартапы", "стартапы на techcrunch", "новые funding rounds", "топ стартапов TechCrunch", "стартапы недели", "парсинг TechCrunch", "анализ TechCrunch", "20 лучших стартапов на TechCrunch" | Папки стартап-проектов в 01-Projects/{slug}/ (_index.md + Анализ для Сергея.md + Russian-аналог концепция.md), сводный отчёт в 02-Areas/Работа/Свои проекты/{date} — TechCrunch анализ.md, state в Meta/states/techcrunch-parser.md | awareness | active |
| dashboard-updater | Обновление дашборда | Читает актуальные данные из дневников (вес, расходы, питание, цели) и обновляет DATA-секцию в Главная страница.md | "обнови дашборд", "обновить главную", "перегенерируй дашборд", "refresh dashboard", "обнови графики", "данные на дашборде устарели", "синхронизируй дашборд" | Обновлённая Главная страница.md с актуальными данными | awareness | active |
| life-analytics | Аналитик жизненных показателей | Читает дневники, расходы, питание, цели за период и формирует подробный мультиролевой обзор (коуч + диетолог + финансист + психолог + аналитик) | "аналитика за период", "обзор за неделю", "обзор за период", "анализ между датами", "сводка за период", "итоги за период", "разбор периода", "еженедельный обзор", "подведи итоги за" | Файл обзора в 02-Areas/Личное/Дневник/2026/{Месяц}/ формата YYYY-MM-DD - YYYY-MM-DD.md | full (постфактум-проверка) | active |
| health-therapist | Врач-терапевт высшей категории + офтальмология (клиническое заключение) | Доказательная интерпретация анализов, УЗИ, заключений врачей, дневников, офтальмологических выписок (3Z PDF). Формирует структурированное заключение (сводка, интерпретация, красные флаги, динамика, рекомендации, резюме, индекс здоровья 0–100). Поддерживает подспециализацию по офтальмологии (миопия, астигматизм, амблиопия, ВГД, ПЗО, оценка LASIK). Работает с данными пациента (общее здоровье + зрение) и отца (миелома) | "клиническое заключение", "терапевт проанализируй", "медицинский разбор", "клиническая интерпретация", "красные флаги", "анализ здоровья", "здоровье отца", "миелома отца", "индекс здоровья", "полное обследование", "зрение", "офтальмология", "миопия", "лазерная коррекция", "острота зрения", "рефракция", "ВГД", "глазное дно" | Клиническое заключение по стандарту: сводка + интерпретация + красные флаги + динамика + рекомендации + резюме + индекс 0–100. Обновляет блок здоровья и блок зрения в дашборде | full | active |
| health-dashboard-updater | Обновление Дашборда здоровья | Парсит новые заключения врачей (терапевт, кардиолог, офтальмолог 3Z, гематолог отца), извлекает показатели (вес, ВГД, рефракция, ПЗО, АД, HbA1c, лабораторные, миеломные маркеры) и обновляет массивы DataviewJS в Дашборд здоровья.md (FACT, WEIGHT_HISTORY, DOCTORS, DIAGNOSES, MED_TIMELINE, FATHER_HEALTH, VISION, HEALTH_GOALS, EYE_DYN, BP_DYN, LAB_DYN, FATHER_DYN, HEALTH_INDEX). Дополняет тайм-линию, графики динамики, плавающие карточки врачей. Цвет акцента — зелёный #22c55e | "обнови дашборд здоровья", "появилось новое заключение врача", "добавь данные офтальмолога", "обнови графики здоровья", "добавь анализы в дашборд", "новый визит к врачу", "обнови блок зрения", "обнови блок давления", "новые лабораторные данные", "обнови индекс здоровья", "пересчитай индекс зрения", "новое медицинское заключение", "добавь врача в дашборд", "обнови карточку врача", "обнови графики ВГД", "добавь точку UCVA", "обнови блок отца", "обнови миелому" | Обновлённый 02-Areas/Личное/Здоровье/Дашборд здоровья.md с актуальными данными, графиками динамики (UCVA, ВГД, ПЗО, вес, HbA1c, АД, M-протеин), карточками врачей, тайм-линией. State в Meta/states/health-dashboard-updater.md | awareness | active |
| folder-icon-updater | Расстановка Lucide-иконок на папки vault | Сканирует файловую систему vault, сравнивает с конфигом obsidian-icon-folder, добавляет недостающие иконки с цветами в формате object | "расставь иконки", "обнови иконки папок", "иконки vault", "проверь иконки" | Обновлённый obsidian-icon-folder/data.json, отчёт о новых/устаревших иконках | awareness | active |
| Skill | Source Agent | Triggers | Purpose | Status |
| `/onboarding` | architect | "initialize the vault", "set up the vault", "onboarding", "vault setup" | Full vault setup conversation | active |
| `/create-agent` | architect | "create a new agent", "custom agent", "I need a new agent", "build an agent", "new crew member" | Custom agent creation (6-phase interview) | active |
| `/manage-agent` | architect | "edit my agent", "update agent", "remove agent", "delete agent", "list agents", "show my agents" | Edit, remove, list custom agents | active |
| `/defrag` | architect | "defragment the vault", "reorganize the vault", "structural maintenance", "vault defrag", "weekly defrag" | Weekly vault defragmentation (5-phase audit) | active |
| `/email-triage` | postman | "check my email", "what's in my inbox", "process emails", "email triage", "anything urgent in email?" | Email scanning, priority scoring, classification | active |
| `/meeting-prep` | postman | "prepare for meeting", "meeting prep", "brief me for the meeting", "get ready for the call" | Comprehensive meeting brief with context gathering | active |
| `/weekly-agenda` | postman | "weekly agenda", "what's this week", "week overview", "plan my week" | Day-by-day week overview from calendar, email, vault | active |
| `/deadline-radar` | postman | "deadline radar", "what are my deadlines", "this week's deadlines", "upcoming deadlines" | Unified deadline timeline with urgency grouping | active |
| `/transcribe` | transcriber | "transcribe", "I have a recording", "process this audio", "meeting notes from recording", "summarize the call" | Audio/transcript processing with structured notes | active |
| `/vault-audit` | librarian | "weekly review", "check the vault", "vault audit", "full audit", "vault health" | Full 7-phase vault audit | active |
| `/deep-clean` | librarian | "deep clean", "deep cleanup", "thorough cleanup", "the vault is a mess" | Extended vault cleanup with stale content detection | active |
| `/tag-garden` | librarian | "tag garden", "clean up tags", "tag cleanup", "tag audit" | Tag analysis: unused, orphan, near-duplicates | active |
| `/inbox-triage` | sorter | "triage the inbox", "clean up the inbox", "sort my notes", "empty inbox", "file my notes", "process the inbox" | Inbox note processing, classification, and routing | active |
<!-- MBIFC:CUSTOM_AGENTS_END -->

---

## Status Values

- **active**: Agent is operational and available for dispatch
- **disabled**: Agent is temporarily disabled — the dispatcher will skip it

---

## How This File Is Used

1. **Dispatcher** reads the `Input` column to match user messages to agents
2. **Dispatcher** reads `Output` + `Capabilities` of other agents to decide if chaining is needed after an agent returns
3. **Agents** reference this file when suggesting next agents in their output
4. **Custom agents** are added as new rows by the Architect during the custom agent creation flow

---

## Custom Agents

Custom agents are created by the Architect through a conversational flow with the user. They follow the exact same schema as core agents and are added as new rows in the Registry table above.

### How Custom Agents Are Added

1. The user asks the Architect to create a new agent (or an existing agent suggests one via `### Suggested new agent`)
2. The Architect conducts a detailed conversation to understand requirements
3. The Architect generates the agent file in `.claude/agents/`, adds a row to the Registry table above, and updates `agents.md`
4. The platform auto-discovers the new agent from its frontmatter

### Naming Rules

- Custom agent names must be lowercase, hyphens only (e.g., `habit-tracker`, `recipe-manager`)
- Names must NOT conflict with core agent names: architect, scribe, sorter, seeker, connector, librarian, transcriber, postman
- Names should be descriptive and concise (1-2 words)

### Priority

Custom agents always have lower routing priority than the 8 core agents. The dispatcher checks custom agents only when no core agent matches the user's message. Among custom agents, the dispatcher uses the Input column to find the best match

---

## Skills Registry

Skills handle complex, multi-step workflows extracted from agents. They are checked **before** agents by the dispatcher (higher priority). Skills run in the main conversation context via the Skill tool, preserving multi-turn state.

| Skill | Source Agent | Triggers | Purpose | Status |
|-------|-------------|----------|---------|--------|
| `/onboarding` | architect | "initialize the vault", "set up the vault", "onboarding", "vault setup" | Full vault setup conversation | active |
| `/create-agent` | architect | "create a new agent", "custom agent", "I need a new agent", "build an agent", "new crew member" | Custom agent creation (6-phase interview) | active |
| `/manage-agent` | architect | "edit my agent", "update agent", "remove agent", "delete agent", "list agents", "show my agents" | Edit, remove, list custom agents | active |
| `/defrag` | architect | "defragment the vault", "reorganize the vault", "structural maintenance", "vault defrag", "weekly defrag" | Weekly vault defragmentation (5-phase audit) | active |
| `/email-triage` | postman | "check my email", "what's in my inbox", "process emails", "email triage", "anything urgent in email?" | Email scanning, priority scoring, classification | active |
| `/meeting-prep` | postman | "prepare for meeting", "meeting prep", "brief me for the meeting", "get ready for the call" | Comprehensive meeting brief with context gathering | active |
| `/weekly-agenda` | postman | "weekly agenda", "what's this week", "week overview", "plan my week" | Day-by-day week overview from calendar, email, vault | active |
| `/deadline-radar` | postman | "deadline radar", "what are my deadlines", "this week's deadlines", "upcoming deadlines" | Unified deadline timeline with urgency grouping | active |
| `/transcribe` | transcriber | "transcribe", "I have a recording", "process this audio", "meeting notes from recording", "summarize the call" | Audio/transcript processing with structured notes | active |
| `/vault-audit` | librarian | "weekly review", "check the vault", "vault audit", "full audit", "vault health" | Full 7-phase vault audit | active |
| `/deep-clean` | librarian | "deep clean", "deep cleanup", "thorough cleanup", "the vault is a mess" | Extended vault cleanup with stale content detection | active |
| `/tag-garden` | librarian | "tag garden", "clean up tags", "tag cleanup", "tag audit" | Tag analysis: unused, orphan, near-duplicates | active |
| `/inbox-triage` | sorter | "triage the inbox", "clean up the inbox", "sort my notes", "empty inbox", "file my notes", "process the inbox" | Inbox note processing, classification, and routing | active |
| `/contact-sync` | postman | "sync contact", "add to contacts", "save contact", "update contact", "is this person in my contacts" | Sync person to Apple Contacts (search, create, update). Requires `apple-contacts` MCP. | active |

### How Skills Are Routed

1. The dispatcher checks the **skill routing table** (in `CLAUDE.md`) before the agent routing table
2. If a trigger matches, the skill is invoked via the **Skill tool** — not the Agent tool
3. If no skill matches, the dispatcher falls through to agent routing
4. Skills can produce `### Suggested next agent` output, which the dispatcher handles using the same chaining rules as agents
