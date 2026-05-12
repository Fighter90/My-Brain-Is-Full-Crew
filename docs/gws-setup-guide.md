# Настройка почтовых бэкендов для агента Postman

Агент Postman поддерживает три почтовых бэкенда. Вы можете использовать один или несколько:

| Бэкенд | Провайдер почты | Уровень доступа | Календарь |
|--------|-----------------|-----------------|-----------|
| **GWS CLI** (`gws`) | Gmail / Google Workspace | Полный read/write | Да (Google Calendar) |
| **Hey CLI** (`hey`) | Hey.com | Полный read/write | Нет (у Hey свои инструменты, не Google Calendar) |
| **MCP-коннекторы** | Gmail | Только чтение + черновики | Только чтение (Google Calendar) |

Если у вас есть и Gmail, и Hey.com — можно использовать `gws` и `hey` одновременно. Предпочитаемый основной бэкенд указывается в `Meta/user-profile.md` через `email_backend: hey` или `email_backend: gws` (по умолчанию — `gws`).

---

## Вариант A: Hey CLI (для пользователей Hey.com)

### Шаг 1: Установка Hey CLI

```bash
# Актуальные инструкции — https://github.com/basecamp/hey-cli
gem install hey-cli
```

### Шаг 2: Аутентификация

```bash
hey auth login
```

### Шаг 3: Проверка

```bash
hey auth status --json
hey box imbox --json --limit 1
```

Если обе команды возвращают JSON — всё готово. Postman автоопределит `hey` в PATH.

### Поиск неисправностей Hey

- **`hey: command not found`**: убедитесь, что bin-директория гема в PATH. Проверьте через `gem environment` и добавьте `<gem_dir>/bin` в PATH.
- **Auth протух**: запустите `hey auth refresh` или `hey auth login`.
- **Общие проблемы**: запустите `hey doctor` для диагностики.

---

## Вариант B: Google Workspace CLI (для пользователей Gmail)

Агент Postman использует [Google Workspace CLI](https://github.com/googleworkspace/cli) (`gws`) для работы с Gmail и Google Calendar. Это даёт агенту полный read/write-доступ — поиск, чтение, архив, удаление, расстановка меток, создание и изменение событий календаря.

### Почему gws, а не MCP?

MCP-серверы Gmail и Calendar, хостящиеся у Anthropic, — read-only (плюс создание черновиков). Они не умеют архивировать, удалять, ставить метки или отправлять письма. Google Workspace CLI оборачивает полный API Google и даёт Postman'у возможность реально управлять инбоксом, а не только читать его.

## Что нужно заранее

- **Node.js** (v18+) и **npm**
- Опционально: **Google Cloud SDK** (`gcloud`) — нужен только если предпочитаете CLI вместо UI Cloud Console
- **Google-аккаунт** (личный Gmail тоже подходит)

## Шаг 1: Установка Google Workspace CLI

```bash
npm install -g @googleworkspace/cli
```

Проверка:

```bash
gws --version
```

## Шаг 2: Установка Google Cloud SDK (если ещё не установлен)

### macOS (Apple Silicon)

```bash
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-darwin-arm.tar.gz
tar -xf google-cloud-cli-darwin-arm.tar.gz
./google-cloud-sdk/install.sh
```

### macOS (Intel)

```bash
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-darwin-x86_64.tar.gz
tar -xf google-cloud-cli-darwin-x86_64.tar.gz
./google-cloud-sdk/install.sh
```

### Другие платформы

Смотрите https://cloud.google.com/sdk/docs/install

После установки перезапустите терминал, чтобы новый PATH подхватился. Если перезапускать не хочется — прогрузите профиль вручную:

```bash
source ~/.zshrc   # или ~/.bashrc
```

Проверка:

```bash
gcloud --version
```

## Шаг 3: Создание проекта Google Cloud

1. Откройте https://console.cloud.google.com/
2. Создайте новый проект (например, `my-vault`)
3. Запомните Project ID — он понадобится ниже

## Шаг 4: Настройка OAuth consent screen

1. Откройте **APIs & Services > OAuth consent screen** в проекте:
   `https://console.cloud.google.com/apis/credentials/consent?project=YOUR_PROJECT_ID`
2. Выберите **External** в качестве User Type (это единственный вариант для личных Gmail-аккаунтов)
3. Заполните обязательные поля:
   - App name: что угодно (например, «Vault CLI»)
   - User support email: ваша почта
   - Developer contact: ваша почта
4. Прокликайте оставшиеся экраны (scopes, test users) и сохраните

**Важно — добавьте себя как тестового пользователя:**

5. Вернитесь на OAuth consent screen, найдите секцию **Audience**
6. В **Test users** нажмите **Add users**
7. Введите свой Gmail-адрес и сохраните

Этот шаг легко пропустить — без него получите ошибку «Access blocked». Неверифицированные приложения могут использовать только явно прописанные тестовые пользователи.

## Шаг 5: Создание OAuth-кредов

1. Откройте **APIs & Services > Credentials**:
   `https://console.cloud.google.com/apis/credentials?project=YOUR_PROJECT_ID`
2. Нажмите **Create Credentials > OAuth client ID**
3. Application type: **Desktop app**
4. Name: что угодно (например, «gws-cli»)
5. Нажмите **Create**
6. Скопируйте **Client ID** и **Client Secret**

## Шаг 6: Настройка аутентификации gws

```bash
gws auth setup
```

Когда запросит — вставьте свой Client ID и Client Secret.

## Шаг 7: Логин и выбор scopes

```bash
gws auth login
```

Откроется интерактивный селектор scopes. **Снимите все галочки** и оставьте только:

- `https://www.googleapis.com/auth/gmail.modify` — чтение/запись/архив/удаление писем
- `https://www.googleapis.com/auth/gmail.send` — отправка писем и черновиков
- `https://www.googleapis.com/auth/calendar.events` — создание/обновление/удаление событий календаря
- `https://www.googleapis.com/auth/calendar.calendarlist.readonly` — список доступных календарей

Опционально можно оставить:

- `https://www.googleapis.com/auth/drive` — если нужен доступ к Drive
- `https://www.googleapis.com/auth/tasks` — если нужен доступ к Tasks
- `openid`, `userinfo.email`, `userinfo.profile` — для информации профиля

**Не выбирайте все 85+ scopes.** Google отклонит запрос для неверифицированных приложений со слишком большим количеством scopes, особенно админских/воркспейсных, недоступных личным аккаунтам.

После выбора scopes откроется окно браузера. Залогиньтесь в Google-аккаунт. Может появиться предупреждение «This app isn't verified» — нажмите **Continue** (это ожидаемо для OAuth-приложений личного использования).

Если всё успешно — увидите:

```
Authentication successful. Encrypted credentials saved.
```

## Шаг 8: Проверка работы

Тест доступа к Gmail:

```bash
gws gmail users messages list --params '{"userId": "me", "maxResults": 3}'
```

Тест доступа к Calendar:

```bash
gws calendar events list --params '{"calendarId": "primary", "timeMin": "2026-03-01T00:00:00Z", "maxResults": 3}'
```

Обе команды должны вернуть JSON.

## Шаг 9: Удаление MCP-серверов (опционально)

Если в `.mcp.json` всё ещё прописаны Gmail/Calendar серверы от Anthropic — их можно удалить. Уберите только записи `gmail` и `google-calendar` из `.mcp.json`, оставив остальные MCP-серверы как есть.

Если `.mcp.json` содержит только эти два сервера — можно удалить файл целиком.

## Поиск неисправностей

### «Access blocked» / Error 403

Вы не добавили себя как тестового пользователя. Вернитесь к Шагу 4, пункты 5–7.

### «invalid_scope» / Error 400

Выбрали слишком много scopes, в том числе недоступных личным Gmail-аккаунтам (admin, classroom, chat и т.д.). Перезапустите `gws auth login` и выберите только scopes из Шага 7.

### «gcloud CLI not found»

Google Cloud SDK не в PATH. Перезапустите терминал. Если не хотите — выполните:

```bash
source ~/google-cloud-sdk/path.zsh.inc   # поправьте путь, если ставили в другое место
```

### команда gws не найдена

Сначала перезапустите терминал — это решает большинство случаев. Если не помогло — глобальная bin-директория npm может быть не в PATH. Проверьте:

```bash
npm config get prefix
```

И убедитесь, что `<prefix>/bin` находится в PATH.

### Предупреждение «Using keyring backend: keyring»

Это нормально — gws хранит зашифрованные креды в системном keyring. Не ошибка.

## Как это работает внутри агента Postman

Агент Postman вызывает команды `gws` через Bash-инструмент. Ключевые операции:

| Операция | Команда |
|----------|---------|
| Поиск в инбоксе | `gws gmail users messages list --params '{"userId": "me", "q": "..."}'` |
| Чтение письма | `gws gmail users messages get --params '{"userId": "me", "id": "ID", "format": "full"}'` |
| Чтение треда | `gws gmail users threads get --params '{"userId": "me", "id": "ID"}'` |
| Пометка прочитанным | `gws gmail users messages modify --params '{"userId": "me", "id": "ID"}' --json '{"removeLabelIds": ["UNREAD"]}'` |
| Архив | `gws gmail users messages modify --params '{"userId": "me", "id": "ID"}' --json '{"removeLabelIds": ["INBOX"]}'` |
| Корзина | `gws gmail users messages trash --params '{"userId": "me", "id": "ID"}'` |
| Список событий | `gws calendar events list --params '{"calendarId": "primary", "timeMin": "...", "timeMax": "..."}'` |
| Создание события | `gws calendar events insert --params '{"calendarId": "primary"}' --json '{"summary": "...", ...}'` |
| Создание черновика | `gws gmail users drafts create --params '{"userId": "me"}' --json '{"message": {"raw": "BASE64"}}'` |

Все команды возвращают JSON. Флаг `--params` — это URL/query-параметры; `--json` — тело запроса.
