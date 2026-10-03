# Assignment 1 — команда Hermes Agent в Telegram

Учебный проект с тремя профилями Hermes и Telegram-ботами: координатор, исследователь и практик. Координатор принимает запрос, при необходимости поручает части задачи специалистам, ждёт их ответы и готовит итог.

## Русский

### Роли

| Профиль | Telegram-бот | Ответственность |
| --- | --- | --- |
| `coordinator` | `@mdlt_coordinator_bot` | Распределяет задачи и объединяет ответы |
| `researcher` | `@mdlt_researcher_bot` | Объясняет теорию и проверяет источники |
| `practitioner` | `@MDLT_practitioner_bot` | Готовит примеры, расчёты и код |

Тексты ролей находятся в `agents/<profile>/SOUL.md`. Это версии для репозитория; скопируйте каждый файл в одноимённый Hermes-профиль.

### Требования

- Hermes Agent установлен на Windows или в WSL2.
- Настроен LLM-провайдер и модель.
- Через `@BotFather` созданы три разных бота. Каждый профиль должен использовать собственный токен.
- Создана приватная Telegram-группа, в которую добавлены эти боты.

### Настройка Hermes-профилей

В PowerShell создайте три профиля, если они ещё не созданы:

```powershell
hermes profile create coordinator
hermes profile create researcher
hermes profile create practitioner
```

Настройте каждый профиль через мастер. Выберите модель/провайдера, затем Telegram, и укажите токен соответствующего бота из BotFather:

```powershell
hermes -p coordinator setup
hermes -p researcher setup
hermes -p practitioner setup
```

Не вставляйте один и тот же токен в несколько профилей. Не отправляйте токены в чат и не коммитьте их.

### Установка SOUL.md

Скопируйте файлы ролей в каталоги профилей Hermes. Типичные пути Windows:

```text
%LOCALAPPDATA%\hermes\profiles\coordinator\SOUL.md
%LOCALAPPDATA%\hermes\profiles\researcher\SOUL.md
%LOCALAPPDATA%\hermes\profiles\practitioner\SOUL.md
```

Файлы в проекте:

```text
agents/coordinator/SOUL.md
agents/researcher/SOUL.md
agents/practitioner/SOUL.md
```

### Настройка Telegram-группы

Для каждого бота в `@BotFather` откройте `/mybots` → выберите бота → **Bot Settings → Group Privacy → Turn off**. Затем удалите бота из группы и добавьте снова, чтобы Telegram применил изменение. Ограничьте доступ к тестовой группе и разрешённым пользователям.

В этой схеме координатор отвечает на обычные сообщения людей, а специалисты — только на прямые упоминания. Настройте фильтр отдельно для каждого профиля:

```powershell
hermes -p coordinator config set TELEGRAM_REQUIRE_MENTION false
hermes -p researcher config set TELEGRAM_REQUIRE_MENTION true
hermes -p practitioner config set TELEGRAM_REQUIRE_MENTION true
```

`TELEGRAM_REQUIRE_MENTION=true` означает, что бот отвечает в группе только при обращении к нему. Для координатора установлено `false`, чтобы он принимал обычные сообщения. Для специалистов установлено `true`, чтобы они отвечали только на адресованные им сообщения.

Чтобы специалисты могли отвечать на поручения координатора, настройте приём сообщений от ботов с обязательным упоминанием:

```powershell
hermes -p coordinator config set TELEGRAM_ALLOW_BOTS mentions
hermes -p coordinator config set TELEGRAM_BOTS_REQUIRE_MENTION true
hermes -p researcher config set TELEGRAM_ALLOW_BOTS mentions
hermes -p researcher config set TELEGRAM_BOTS_REQUIRE_MENTION true
hermes -p practitioner config set TELEGRAM_ALLOW_BOTS mentions
hermes -p practitioner config set TELEGRAM_BOTS_REQUIRE_MENTION true
```

Сверьте доступность этих параметров через `hermes config --help` и документацию установленной версии Hermes. В каждом профиле должны быть разрешены ваш Telegram user ID или тестовая группа.

### Запуск и проверка

На одном компьютере именованные профили обслуживаются общим gateway. Не запускайте отдельный gateway для каждого профиля.

```powershell
hermes gateway install
hermes gateway start
hermes gateway status
```

Проверьте личные сообщения каждому боту. Затем напишите в группе каждому боту с его `@username`. Для проверки делегирования напишите только координатору — не упоминайте специалистов в исходном запросе. Успешная демонстрация: координатор сам публикует поручения с упоминаниями специалистов, получает их ответы и формирует итог.

### Диагностика

```powershell
hermes gateway status
hermes -p coordinator logs errors --since 30m
hermes -p researcher logs errors --since 30m
hermes -p practitioner logs errors --since 30m
```

- Бот не отвечает в группе: проверьте Group Privacy, повторно добавьте бота и проверьте список разрешённых пользователей/групп.
- Несколько ботов отвечают всем: проверьте `TELEGRAM_REQUIRE_MENTION` в каждом профиле.
- Специалист отвечает сам, но не получает поручений: проверьте настройки приёма сообщений от ботов и активный `SOUL.md`.
- Ошибка `No LLM provider configured`: настройте модель именно для профиля из ошибки.
- Ошибка `duplicate credential`: назначьте каждому боту отдельный Telegram-токен.

### Безопасность

Не добавляйте в Git токены, API-ключи, `.env`, приватные логи или личные данные. Если токен утёк, отзовите его через BotFather и настройте новый.

## English

### Agent roles

| Profile | Telegram bot | Responsibility |
| --- | --- | --- |
| `coordinator` | `@mdlt_coordinator_bot` | Delegates tasks and combines replies |
| `researcher` | `@mdlt_researcher_bot` | Explains theory and checks sources |
| `practitioner` | `@MDLT_practitioner_bot` | Prepares examples, calculations, and code |

Role prompts are in `agents/<profile>/SOUL.md`. These are the repository copies; copy each file into the matching Hermes profile.

### Requirements

- Hermes Agent installed on Windows or WSL2.
- An LLM provider and model configured.
- Three separate Telegram bots created with `@BotFather`. Each profile must use its own bot token.
- A private Telegram group containing the three bots.

### Configure Hermes profiles

In PowerShell, create the profiles if they do not already exist:

```powershell
hermes profile create coordinator
hermes profile create researcher
hermes profile create practitioner
```

Run setup for each profile. Choose a model/provider, then Telegram, and enter the token for the matching BotFather bot:

```powershell
hermes -p coordinator setup
hermes -p researcher setup
hermes -p practitioner setup
```

Never reuse one token across profiles. Do not post tokens in chats or commit them.

### Install the SOUL.md files

Copy each role prompt into the corresponding Hermes profile directory. Typical Windows paths:

```text
%LOCALAPPDATA%\hermes\profiles\coordinator\SOUL.md
%LOCALAPPDATA%\hermes\profiles\researcher\SOUL.md
%LOCALAPPDATA%\hermes\profiles\practitioner\SOUL.md
```

Repository prompt files:

```text
agents/coordinator/SOUL.md
agents/researcher/SOUL.md
agents/practitioner/SOUL.md
```

### Configure the Telegram group

For each bot, open `@BotFather` → `/mybots` → select the bot → **Bot Settings → Group Privacy → Turn off**. Then remove and re-add the bot to the group so Telegram applies the change. Restrict access to the test group and authorized users.

Enable explicit mentions and deterministic bot routing for every profile:

```powershell
hermes -p coordinator config set TELEGRAM_REQUIRE_MENTION false
hermes -p researcher config set TELEGRAM_REQUIRE_MENTION true
hermes -p practitioner config set TELEGRAM_REQUIRE_MENTION true
```

`TELEGRAM_REQUIRE_MENTION=true` means a bot responds in the group only when addressed. This setup uses `false` for the coordinator so it can answer ordinary human messages, and `true` for both specialists so they only answer direct mentions.

Allow bot-authored assignments and replies, requiring explicit mentions:

```powershell
hermes -p coordinator config set TELEGRAM_ALLOW_BOTS mentions
hermes -p coordinator config set TELEGRAM_BOTS_REQUIRE_MENTION true
hermes -p researcher config set TELEGRAM_ALLOW_BOTS mentions
hermes -p researcher config set TELEGRAM_BOTS_REQUIRE_MENTION true
hermes -p practitioner config set TELEGRAM_ALLOW_BOTS mentions
hermes -p practitioner config set TELEGRAM_BOTS_REQUIRE_MENTION true
```

Check that these settings are supported by your installed Hermes version using `hermes config --help` and the matching documentation. Each profile must authorize your Telegram user ID or the test group.

### Start and verify

On one computer, named profiles are served by the shared gateway. Do not start a separate gateway for every profile.

```powershell
hermes gateway install
hermes gateway start
hermes gateway status
```

Test a private message to each bot, then mention each bot by its `@username` in the group. To test delegation, send a request to the coordinator only; do not mention the specialists in the original request. A successful demo shows the coordinator posting addressed assignments, receiving the specialists' replies, and producing a final answer.

### Troubleshooting

```powershell
hermes gateway status
hermes -p coordinator logs errors --since 30m
hermes -p researcher logs errors --since 30m
hermes -p practitioner logs errors --since 30m
```

- Bot does not respond in the group: check Group Privacy, re-add the bot, and verify the user/group allowlist.
- All bots answer every message: check `TELEGRAM_REQUIRE_MENTION` in every profile.
- A specialist works in DMs but does not receive assignments: check bot-message settings and the active `SOUL.md`.
- `No LLM provider configured`: configure a model for the profile named in the error.
- `duplicate credential`: give every bot a unique Telegram token.

### Security

Do not commit bot tokens, API keys, `.env` files, private logs, or personal data. If a token leaks, revoke it through BotFather and configure a replacement.

## Официальная документация / Official documentation

- [Профили Hermes / Hermes profiles](https://hermes-agent.nousresearch.com/docs/user-guide/profiles/)
- [Telegram в Hermes / Hermes Telegram](https://hermes-agent.nousresearch.com/docs/user-guide/messaging/telegram/)
