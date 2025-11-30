---
description: Безопасный деплой с проверками
argument-hint: [staging|production]
---

Выполни деплой на окружение: $1

## Pre-deployment чеклист:

### 1. Проверка кода
```bash
# Запусти линтеры
npm run lint  # или yarn lint, eslint, etc

# Проверь типы (для TypeScript)
npx tsc --noEmit

# Проверь форматирование
npm run format:check
```

### 2. Тестирование
```bash
# Запусти все тесты
npm test

# Проверь покрытие
npm run test:coverage

# E2E тесты
npm run test:e2e
```

### 3. Сборка
```bash
# Создай production build
npm run build

# Проверь что сборка успешна
ls -lh dist/
```

### 4. Проверка зависимостей
```bash
# Проверь на уязвимости
npm audit

# Обнови критические зависимости если нужно
npm audit fix
```

### 5. Проверка переменных окружения
- Все ли ENV переменные установлены?
- Правильные ли значения для $1?
- Нет ли хардкоженых секретов?

### 6. Database migrations
```bash
# Если есть миграции БД
npm run migrate:$1
```

## Deployment процесс:

### Для Staging:
```bash
# 1. Переключись на правильную ветку
git checkout develop

# 2. Убедись что ветка актуальна
git pull origin develop

# 3. Задеплой
# (команда зависит от вашего setup)
npm run deploy:staging
# или
./scripts/deploy-staging.sh
# или через CI/CD
git push origin develop
```

### Для Production:
```bash
# 1. Создай release ветку
git checkout -b release/v1.2.3 main

# 2. Обнови версию
npm version patch  # или minor, или major

# 3. Создай тег
git tag -a v1.2.3 -m "Release v1.2.3"

# 4. Запуш изменения
git push origin release/v1.2.3 --tags

# 5. Создай PR в main
gh pr create --base main --title "Release v1.2.3"

# 6. После мерджа PR деплой произойдет автоматически через CI/CD
```

## Post-deployment проверки:

### 1. Health check
```bash
# Проверь что приложение запустилось
curl https://api.$1.example.com/health

# Проверь версию
curl https://api.$1.example.com/version
```

### 2. Smoke tests
- Основной функционал работает?
- Пользователи могут войти?
- API отвечает?
- База данных доступна?

### 3. Мониторинг
- Проверь логи на ошибки
- Проверь метрики (CPU, память, запросы)
- Настрой алерты если нужно

### 4. Уведомления
```bash
# Уведоми команду
# Slack
curl -X POST -H 'Content-type: application/json' \
  --data '{"text":"✅ Deployed to $1: v1.2.3"}' \
  $SLACK_WEBHOOK_URL

# Email, PagerDuty, etc
```

## Rollback план:

Если что-то пошло не так:

### Быстрый откат:
```bash
# Откат к предыдущей версии
kubectl rollout undo deployment/app-name
# или
heroku releases:rollback
# или
./scripts/rollback.sh
```

### Полный откат:
```bash
# 1. Ревёрт коммита
git revert HEAD

# 2. Запуш
git push origin main

# 3. Триггер деплоя
# CI/CD сделает автоматически
```

## Важно:

⚠️ **Никогда не деплой в production в пятницу вечером!**
⚠️ **Всегда тестируй на staging перед production**
⚠️ **Имей план отката**
⚠️ **Уведоми команду о деплое**
⚠️ **Мониторь приложение после деплоя**

## Результат:

После успешного деплоя:
- ✅ Все тесты прошли
- ✅ Сборка успешна
- ✅ Приложение запущено на $1
- ✅ Health checks проходят
- ✅ Команда уведомлена
- ✅ Документация обновлена (release notes)
