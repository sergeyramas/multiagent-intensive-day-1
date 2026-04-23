# 🎓 Мультиагенты Интенсив — Урок 1: AI-Секретарь через Superpowers

> **Правильный процесс эфира:** пишешь вольное ТЗ → `/brainstorm` → `/writing-plans` → `/subagent-driven-development` → система **сама** генерирует агентов. Не копипаст, а полный workflow.

[![Claude Code](https://img.shields.io/badge/Claude_Code-Opus_4.7-7C3AED?style=flat-square)](https://claude.ai/code)
[![Superpowers](https://img.shields.io/badge/framework-Superpowers-0EA5E9?style=flat-square)](https://github.com/obra/superpowers)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](./LICENSE)
[![GitHub Pages](https://img.shields.io/badge/site-GitHub_Pages-222?style=flat-square)](https://sergeyramas.github.io/multiagent-intensive-day-1/)

## ⚠️ Два правила, которые нельзя нарушать

1. **`Instructions.md` пишется ТОЛЬКО РУКАМИ.** Не ChatGPT, не шаблон, не копипаст. Вольный текст, своими словами — хотелки, боли, роли. Процесс опирается на твоё ЛИЧНОЕ ТЗ, а не на «идеальный» шаблон.
2. **Каждый шаг Superpowers — в НОВОМ диалоге.** Перед `/brainstorm` — новый. Перед `/writing-plans` — новый. Перед `/subagent-driven-development` — новый. Иначе контекст засоряется.

## Что в этом репозитории

```
.
├── README.md                 ← этот урок (24 шага)
├── LICENSE
├── _config.yml               ← GitHub Pages (Cayman theme)
│
├── examples/                 ← реальные артефакты реального запуска
│   ├── Instructions.md       ← пример вольного ТЗ (12 КБ, рукопись)
│   ├── Information.md        ← справочные данные
│   └── docs/
│       └── superpowers/
│           ├── specs/*.md    ← пример результата brainstorm (27 КБ)
│           └── plans/*.md    ← пример результата writing-plans (69 КБ)
│
└── templates/                ← результат subagent-driven-development
    ├── CLAUDE.md
    ├── artifacts.md
    └── .claude/
        ├── settings.local.json
        └── agents/
            ├── lawyer.md
            ├── finance.md
            ├── researcher.md
            └── assistant.md
```

**Как использовать:**
- **Учишься по эфиру** → читай README, иди по шагам, пиши своё ТЗ, запускай Superpowers
- **Посмотреть, как выглядит результат** → смотри `examples/` и `templates/`
- **План Б (сломалось что-то)** → используй `templates/` как резервный шаблон

---

## 📋 Оглавление

- [Блок 0. Подготовка (Шаги 1–5)](#блок-0--подготовка-среды)
- [Блок 1. Папка проекта (Шаги 6–7)](#блок-1--папка-проекта)
- [Блок 2. Superpowers (Шаг 8)](#блок-2--superpowers)
- [Блок 3. Напиши `Instructions.md` (Шаг 9)](#блок-3--напиши-instructionsmd)
- [Блок 4. `/brainstorm` → спека (Шаги 10–11)](#блок-4--brainstorm--спека)
- [Блок 5. `/writing-plans` → план (Шаги 12–13)](#блок-5--writing-plans--план)
- [Блок 6. `/subagent-driven-development` → агенты (Шаг 14)](#блок-6--subagent-driven-development--агенты)
- [Блок 7. MCP Google Workspace (Шаги 15–17)](#блок-7--mcp-google-workspace)
- [Блок 8. Проверка системы (Шаги 18–22)](#блок-8--проверка-системы)
- [Блок 9. Сдача домашки (Шаги 23–24)](#блок-9--сдача-домашки)
- [Частые проблемы](#частые-проблемы)

---

## Блок 0 — Подготовка среды

### Шаг 1. Установи Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Если нет `npm` → `brew install node` (macOS) или https://nodejs.org.

**Проверка:** `claude --version`

---

### Шаг 2. Авторизуйся

```bash
claude login
```

Откроется браузер → войди в Anthropic.

---

### Шаг 3. VSCode + расширения

Скачай https://code.visualstudio.com. `⌘+Shift+X` → «Claude Code» (от Anthropic).

Также рекомендую:
- **Claude Notifier** — уведомления
- **Office Viewer** — .xlsx/.docx в VSCode
- **Claude Monkey Bar** — статус-бар

---

### Шаг 4. Модель + effort

`claude` → `/config`:

- **Max подписка:** `claude-opus-4-7` + `effort: Extra High`
- **Pro подписка:** `claude-sonnet-4-6` + `effort: High`

`/exit`

---

### Шаг 5. Включи Bypass Permissions

Без этого Claude задаёт подтверждения на каждую команду. Создай глобальный `~/.claude/settings.json`:

<details>
<summary><b>📋 Содержимое <code>~/.claude/settings.json</code></b></summary>

```json
{
  "permissions": {
    "allow": [
      "Bash(mkdir *)",
      "Bash(ls *)",
      "Bash(cat *)",
      "Bash(touch *)",
      "Bash(git *)",
      "Bash(npm *)",
      "Bash(node *)",
      "Bash(python3 *)",
      "Bash(claude mcp *)",
      "Read(*)",
      "Write(*)",
      "Edit(*)"
    ],
    "ask": [],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(sudo *)",
      "Bash(curl * | sh)"
    ]
  }
}
```

</details>

---

## Блок 1 — Папка проекта

### Шаг 6. Создай папку

```bash
mkdir -p ~/Documents/AI-Секретарь
cd ~/Documents/AI-Секретарь
```

---

### Шаг 7. Минимальная структура

```bash
mkdir -p docs
```

**Важно:** `.claude/agents/` **не создаём руками** — её сгенерирует subagent-driven-development на шаге 14.

---

## Блок 2 — Superpowers

### Шаг 8. Установи плагин

```bash
cd ~/Documents/AI-Секретарь
claude
```

В сессии:

```
/plugins
```

1. `Add Plugin`
2. `obra/superpowers`
3. Install

**Проверка:** `/help` → должны быть `superpowers:brainstorming`, `superpowers:writing-plans`, `superpowers:subagent-driven-development`.

`/exit`

---

## Блок 3 — Напиши `Instructions.md`

### Шаг 9. Своё ТЗ вольным текстом

**Главный шаг урока.**

```bash
code docs/Instructions.md
```

Пиши **своими словами**. Никакого ChatGPT. Вольный текст. Что хочешь, зачем, какие роли.

**Что включить:**
- Главная боль — зачем тебе система
- Агенты — сколько и какие роли
- Для каждого: что должен уметь, какие инструменты
- Примеры запросов пользователя (помогает)

**Посмотри реальный пример** — 12 КБ вольного текста, который стал стартом этого проекта:

**📄 [examples/Instructions.md](./examples/Instructions.md)**

Превью:

<details>
<summary><b>📋 Начало реального <code>Instructions.md</code></b></summary>

```markdown
Мне необходимо создать агентную систему "AI-Секретарь"

Смысл заключается в том, что это будет мой личный исполнительный ассистент.
Делает руками за меня всю рутину: документы, учёт денег, поиск информации,
напоминания.

У меня есть главная боль - не успеваю делать все руками

# CONTEXT:
Должна быть команда из 6 агентов

Координатор — главный мозг, читает мои сообщения и решает, кого звать
Юрист — договоры, акты, КП, счета в Word - сразу на Google Диск
Финансовый агент — учёт доходов и расходов из сообщений, отчёты
Ресёрч-агент — ходит в интернет, находит информацию
Ассистент — Записывает встречи, управляет временем, календарем

## Юрист:
Это должен быть профессиональный юрист в Российской Федерации
с 15-летним стажем...

[и так для каждого агента — детально, своими словами, все режимы работы,
инструменты, конкретные сценарии]
```

</details>

### Опционально: `Information.md` — справочные данные

Если есть справочники (прайсы, списки провайдеров, ценовые данные) — положи в `docs/Information.md`. Brainstorm учтёт.

**📄 [examples/Information.md](./examples/Information.md)**

---

## Блок 4 — `/brainstorm` → спека

### Шаг 10. НОВЫЙ диалог

```bash
cd ~/Documents/AI-Секретарь
claude
```

Старая сессия → `/exit` сначала.

**Проверь:** `/config` → модель + effort всё ещё настроены.

---

### Шаг 11. Запусти brainstorm

```
/brainstorm @docs/Instructions.md
```

**Что произойдёт:**

1. Superpowers читает твой Instructions.md
2. Задаёт **5–10 уточняющих вопросов** (обычно A/B/C/D)
3. Ты отвечаешь
4. Создаёт **спецификацию** (PRD)

**Типичные вопросы:**
- «Количество агентов: A) 3 B) 5 C) 7»
- «Механика: A) sub-agents B) teammates C) гибрид»
- «Артефакты: A) общий файл B) БД C) git»
- «Подтверждения: A) автономно B) через user C) гибрид»
- «Порядок сборки: A) постепенно B) сразу всё»

**Результат:** `docs/superpowers/specs/<дата>-<название>.md` — 200–400 строк.

**Посмотри реальный пример результата brainstorm** (твой):

**📄 [examples/docs/superpowers/specs/2026-04-21-ai-secretary-design.md](./examples/docs/superpowers/specs/2026-04-21-ai-secretary-design.md)** (27 КБ, с архитектурной диаграммой, таблицей решений, политикой подтверждений)

**⚠️ НЕ переходи дальше, если спека тебе не нравится.** Попроси brainstorm отредактировать или запусти заново.

---

## Блок 5 — `/writing-plans` → план

### Шаг 12. НОВЫЙ диалог

`/exit` → `claude`.

---

### Шаг 13. Запусти writing-plans

```bash
ls docs/superpowers/specs/
```

```
/writing-plans @docs/superpowers/specs/<имя-файла>.md
```

**Что произойдёт:**

1. Читает спеку
2. Разбивает на **8–15 задач** (tasks)
3. Определяет порядок и зависимости
4. Сохраняет в `docs/superpowers/plans/<дата>-<название>-implementation.md`

**Типичные задачи:**
```
Task 1: Scaffold directory structure
Task 2: Create artifacts.md catalog
Task 3: Write lawyer.md
Task 4: Write researcher.md
Task 5: Write finance.md
Task 6: Write assistant.md
Task 7: Write CLAUDE.md coordinator
Task 8: Smoke test
```

**Посмотри реальный пример плана** (твой):

**📄 [examples/docs/superpowers/plans/2026-04-21-ai-secretary-implementation.md](./examples/docs/superpowers/plans/2026-04-21-ai-secretary-implementation.md)** (69 КБ, каждый task с детальными шагами, critical files, verification)

---

## Блок 6 — `/subagent-driven-development` → агенты

### Шаг 14. НОВЫЙ диалог + автогенерация

`/exit` → `claude`.

```
/subagent-driven-development @docs/superpowers/plans/<имя-файла>.md
```

**Что произойдёт — магия:**

1. Claude разбирает план
2. **Для каждой задачи поднимает отдельный sub-agent** в изолированном контексте
3. Sub-agent делает свою часть (создаёт файл, проверяет)
4. Checkpoints — иногда запрашивает твоё подтверждение
5. **Автоматически создаются:**
   - `.claude/agents/lawyer.md`
   - `.claude/agents/researcher.md`
   - `.claude/agents/finance.md`
   - `.claude/agents/assistant.md`
   - `CLAUDE.md`
   - `artifacts.md`
   - Папки `inbox/`, `reports/`, `templates/`

**Время:** 20–60 минут.

**Твоя роль:** отвечать на checkpoint-вопросы, ждать.

**Посмотри, как выглядит результат генерации** (твой реальный):

**📁 [templates/](./templates/)** — полный набор: CLAUDE.md, 4 агента, artifacts.md, settings

**Проверка:**

```bash
ls -la .claude/agents/
ls -la
```

---

## Блок 7 — MCP Google Workspace

### Шаг 15. Установка

```bash
mkdir -p ~/tools
cd ~/tools
git clone https://github.com/taylorwilsdon/google_workspace_mcp.git
cd google_workspace_mcp
```

Следуй README репо (`npm install` / `pip install`).

---

### Шаг 16. OAuth

При первом запуске MCP откроется браузер. Разреши:
- ✅ Sheets
- ✅ Docs
- ✅ Drive
- ✅ Calendar

---

### Шаг 17. Регистрация

```bash
cd ~/Documents/AI-Секретарь
claude mcp add google-workspace -- node ~/tools/google_workspace_mcp/dist/index.js
claude mcp list
```

Должно показать `google-workspace: ✓ Connected`.

---

## Блок 8 — Проверка системы

### Шаг 18. Reload window

VSCode → `⌘+Shift+P` → `Developer: Reload Window`.

---

### Шаг 19. Smoke

```
Какие у тебя есть агенты?
```

Координатор отвечает списком по `CLAUDE.md`.

---

### Шаг 20. Инициализация

```
Инициализируй систему
```

Координатор создаёт Google Sheets + Drive-папку, пишет URL в `artifacts.md`.

---

### Шаг 21. Боевой тест (пример с эфира)

```
Мне необходимо составить договор с моим контрагентом, который мне продаёт
башенные краны. Я хочу закупить у него три башенных крана Liebherr 132 EC-H.
Общая сумма сделки 150 миллионов рублей.
```

**Должно произойти:**
1. Координатор → lawyer (+ researcher параллельно)
2. researcher собирает рыночный контекст
3. lawyer задаёт уточнения
4. После ответов — договор в Google Docs
5. URL в `artifacts.md`

---

### Шаг 22. Тесты остальных агентов

```
Запиши расход 500 ₽ на кофе                → finance
Сравни тарифы Timeweb и Beget              → researcher
Что у меня завтра?                          → assistant
```

---

## Блок 9 — Сдача домашки

### Шаг 23. Скринкаст 5–10 мин

Показать:
1. `docs/Instructions.md` — твоё ТЗ
2. Сгенерированную спеку
3. Сгенерированный план
4. Созданные агенты в `.claude/agents/`
5. Live-демо боевого запроса
6. Обновлённый `artifacts.md`

**Как:** QuickTime (macOS), Win+G (Windows), OBS Studio.

---

### Шаг 24. Бот практикума

Ссылка появится в канале. Видео + описание + опц. ссылка на GitHub-репо.

**Дедлайн:** пятница.
**Голосование:** суббота.
**Приз:** MacBook Air 15".

---

## 🎉 Главное отличие этого процесса

| Этап | Что делал ты | Что сделала система |
|---|---|---|
| 1. ТЗ | Написал руками | — |
| 2. Brainstorm | Ответил на вопросы | Создала спеку (27 КБ) |
| 3. Plan | — | Создала план (69 КБ) |
| 4. Impl | Подтверждал чекпоинты | Сама создала ВСЕ файлы агентов |

**Superpowers-workflow: ты даёшь видение, система даёт дисциплину и исполнение.**

---

## Частые проблемы

| Симптом | Решение |
|---|---|
| `/brainstorm` не видит Instructions.md | Проверь путь: `@docs/Instructions.md` от корня проекта |
| Brainstorm задаёт общие вопросы | Instructions.md слишком абстрактный — перепиши детальнее |
| Спека не нравится | Запусти brainstorm заново, добавь контекст в Instructions.md |
| writing-plans ругается на большой spec | Попроси разбить или уточни scope |
| subagent-dev завис | Новый диалог → `/executing-plans` на том же плане — продолжит с checkpoint |
| Агент не создаётся | Task помечен done? Если нет — попроси переделать task |
| Координатор не видит агентов | Reload Window в VSCode |
| `claude mcp list` пустой | Шаги 15–17 |

---

## Ссылки

- **Claude Code:** https://claude.ai/code
- **Superpowers:** https://github.com/obra/superpowers
- **Skills каталог:** https://skills.sh/
- **MCP каталог:** https://smithery.ai/
- **Google Workspace MCP:** https://github.com/taylorwilsdon/google_workspace_mcp
- **3-часовой курс Claude Code:** https://youtu.be/uTX807i8wvA

---

## License

MIT — см. [LICENSE](./LICENSE).

---

*Собрано на основе первого дня курса «Мультиагенты Интенсив». Процесс восстановлен по транскрибации эфира Никиты.*
