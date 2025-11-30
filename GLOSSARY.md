# Глоссарий терминов Claude Code

Справочник терминов и концепций, используемых в Claude Code.

## A

### Agent (Агент)
ИИ-система, способная автономно выполнять задачи, используя доступные инструменты и принимая решения на основе контекста.

### Agent SDK (Claude Agent SDK)
Набор инструментов для разработки агентных приложений на базе Claude.

### Anthropic
Компания-разработчик Claude и Claude Code.

### API Key
Ключ для доступа к Anthropic API. Используется для аутентификации при работе через pay-as-you-go модель.

### Auto-compact
Автоматическое сжатие контекста при приближении к лимиту токенов.

## B

### Bash Tool
Инструмент для выполнения bash-команд в терминале. Например: `!git status`, `!npm test`.

### Best Practices (Лучшие практики)
Проверенные способы и рекомендации для эффективного использования Claude Code.

## C

### CLAUDE.md
Специальный файл в корне проекта, который Claude Code читает автоматически для понимания контекста проекта.

### .claudeignore
Файл-список исключений (как .gitignore), указывает какие файлы Claude Code должен игнорировать.

### CLI (Command-Line Interface)
Интерфейс командной строки для работы с Claude Code через терминал.

### Compact
Команда `/compact` для сжатия контекста с сохранением важной информации. Помогает освободить место для новых задач.

### Context (Контекст)
Вся информация, доступная Claude в текущей сессии: файлы, история диалога, выполненные команды.

### Context Window
Максимальный размер контекста в токенах (обычно 200K для Claude Code).

## D

### Dangerously Skip Permissions
Режим без запросов разрешений. Использовать осторожно, только в доверенном окружении.

## E

### Edit Tool
Инструмент для редактирования существующих файлов. Claude вносит изменения точечно, заменяя конкретные участки кода.

### Export
Команда `/export` для сохранения истории разговора в файл.

## F

### File Reference
Ссылка на файл через символ `@`. Например: `@api/users.ts`.

## G

### Git Integration
Встроенная поддержка git операций: коммиты, ветки, PR, разрешение конфликтов.

### Glob Tool
Инструмент для поиска файлов по паттернам (например: `**/*.ts`).

### Grep Tool
Инструмент для поиска текста в файлах по регулярным выражениям.

## H

### Headless Mode
Режим работы без интерактивного интерфейса. Вывод результата сразу в терминал. Используется в скриптах и CI/CD.

### Hooks (Хуки)
Shell-команды, автоматически выполняемые в определенные моменты (sessionStart, preEditAccept, postEditAccept).

### Hot Keys (Горячие клавиши)
Клавиатурные сочетания для быстрого доступа к функциям (Escape, Tab, Shift+Tab).

## I

### Interactive Mode
Режим диалога с Claude Code в терминале. Основной способ работы.

## J

### JWT (JSON Web Token)
Токен для аутентификации, часто используется в веб-приложениях (контекст примеров в документации).

## M

### MCP (Model Context Protocol)
Протокол для расширения возможностей Claude Code через внешние серверы и интеграции.

### MCP Server
Сервер, предоставляющий дополнительные инструменты через MCP (базы данных, API, сервисы).

### Model
Версия Claude используемая для задачи:
- **Sonnet** — быстрая и экономичная
- **Opus** — мощная для сложных задач

## O

### Opus
Самая мощная модель Claude. Медленнее и дороже Sonnet, но лучше справляется со сложными задачами.

## P

### Permissions (Разрешения)
Система контроля доступа Claude Code к файлам и командам. Настраивается в `.claude/settings.json`.

### Prompt
Запрос или инструкция для Claude Code.

## R

### Read Tool
Инструмент для чтения содержимого файлов.

### Resume
Команда `/resume` для возобновления предыдущей сессии с сохранением контекста.

### Rewind
Команда `/rewind` или `Escape (x2)` для отката изменений к предыдущему состоянию.

## S

### Session (Сессия)
Один запуск Claude Code. Включает всю историю диалога и изменений до выхода из программы.

### Slash Command
Команда начинающаяся с `/`. Например: `/help`, `/clear`, `/compact`.

### Sonnet
Быстрая и экономичная модель Claude. Оптимальный выбор для большинства задач.

### Subagent (Субагент)
Специализированный агент с собственными инструкциями для конкретных задач (code-reviewer, test-writer).

## T

### Token
Единица измерения текста для ИИ моделей. Примерно 1 token ≈ 0.75 слова на английском или ~1 символ в коде.

### Tool
Инструмент, доступный Claude для выполнения задач: Read, Write, Edit, Bash, Glob, Grep и др.

## U

### User Prompt
Сообщение пользователя для Claude Code.

## W

### Write Tool
Инструмент для создания новых файлов.

### Workflow
Последовательность действий для выполнения задачи (например: чтение кода → анализ → изменения → тесты → коммит).

## Концепции и паттерны

### AAA Pattern (Arrange-Act-Assert)
Паттерн написания тестов:
- **Arrange** — подготовка данных
- **Act** — выполнение действия
- **Assert** — проверка результата

### Agent Loop
Цикл работы агента: получение задачи → планирование → выполнение → проверка → завершение или итерация.

### Context Management
Управление размером и содержимым контекста для оптимальной работы Claude Code.

### DRY (Don't Repeat Yourself)
Принцип избегания дублирования кода.

### KISS (Keep It Simple, Stupid)
Принцип простоты решений.

### N+1 Problem
Проблема производительности БД: выполнение N+1 запросов вместо одного с JOIN.

### OWASP Top 10
Список из 10 самых критичных уязвимостей безопасности веб-приложений.

### Repository Pattern
Паттерн проектирования для изоляции логики работы с данными.

### SOLID Principles
Пять принципов объектно-ориентированного программирования:
- **S**ingle Responsibility
- **O**pen/Closed
- **L**iskov Substitution
- **I**nterface Segregation
- **D**ependency Inversion

### SQL Injection
Тип атаки через внедрение вредоносного SQL кода.

### XSS (Cross-Site Scripting)
Тип атаки через внедрение скриптов на веб-страницы.

## Команды и опции CLI

### `claude`
Основная команда для запуска Claude Code в интерактивном режиме.

### `claude -p "prompt"`
Headless режим с прямым выполнением промпта.

### `claude --model opus|sonnet`
Выбор модели для использования.

### `claude --add-dir <path>`
Добавление дополнительных директорий в контекст.

### `claude doctor`
Диагностика установки и конфигурации.

### `claude --dangerously-skip-permissions`
Режим без запроса разрешений (опасно!).

## Файлы конфигурации

### `.claude/settings.json`
Основной файл настроек проекта.

### `.claude/commands/`
Директория с пользовательскими slash-командами.

### `.claude/agents/`
Директория с определениями субагентов.

### `.mcp.json`
Конфигурация MCP-серверов.

### `.env`
Переменные окружения (НИКОГДА не коммитить!).

## Аббревиатуры

- **AI** — Artificial Intelligence (Искусственный интеллект)
- **API** — Application Programming Interface
- **CLI** — Command-Line Interface
- **CI/CD** — Continuous Integration/Continuous Deployment
- **CRUD** — Create, Read, Update, Delete
- **DB** — Database
- **E2E** — End-to-End (тестирование)
- **ENV** — Environment (переменные окружения)
- **FAQ** — Frequently Asked Questions
- **GPG** — GNU Privacy Guard
- **HTTP** — Hypertext Transfer Protocol
- **HTTPS** — HTTP Secure
- **IDE** — Integrated Development Environment
- **JWT** — JSON Web Token
- **LLM** — Large Language Model
- **MCP** — Model Context Protocol
- **ORM** — Object-Relational Mapping
- **PR** — Pull Request
- **REST** — Representational State Transfer
- **SDK** — Software Development Kit
- **SQL** — Structured Query Language
- **SSH** — Secure Shell
- **TDD** — Test-Driven Development
- **TTL** — Time To Live
- **UI** — User Interface
- **URL** — Uniform Resource Locator
- **WSL** — Windows Subsystem for Linux
- **XSS** — Cross-Site Scripting

---

**Версия:** 2.0
**Последнее обновление:** Ноябрь 2025

Не нашли нужный термин? Откройте issue в репозитории!
