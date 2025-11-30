# Workflow: Исправление бага в продакшене

Быстрое и надежное исправление критического бага с использованием Claude Code.

## Сценарий

🚨 **Критический баг:** Пользователи не могут войти в систему после последнего деплоя.

## Шаг 1: Получение информации об ошибке

### От пользователей/поддержки:
- "После ввода правильного пароля показывается 'Invalid credentials'"
- "Проблема началась ~30 минут назад"
- "Затронуты все пользователи"

### Из Sentry/логов:
```
Error: JWT token validation failed
  at verifyToken (auth.ts:45)
  at authenticateUser (auth.ts:120)
```

## Шаг 2: Анализ проблемы с Claude

```
> У нас критический баг в продакшене. Пользователи не могут войти.
>
> Симптомы:
> - "Invalid credentials" при правильном пароле
> - Началось 30 минут назад
> - Затронуты все пользователи
>
> Ошибка из Sentry:
> Error: JWT token validation failed at verifyToken (auth.ts:45)
>
> Последний деплой был час назад.
> Что изменилось: обновили библиотеку jsonwebtoken с 8.5.1 на 9.0.0

Claude анализирует:
1. Читает файл auth.ts
2. Проверяет changelog jsonwebtoken
3. Находит breaking changes в v9.0.0
```

**Результат анализа:**
```
Проблема найдена!

В jsonwebtoken v9.0.0 изменился API:
- Старое: jwt.verify(token, secret)
- Новое: jwt.verify(token, secret, { algorithms: ['HS256'] })

Без указания algorithms выбрасывается ошибка.
```

## Шаг 3: Быстрая проверка локально

```
> Воспроизведи проблему локально

Claude:
1. Читает тесты для auth
2. Находит тест для verifyToken
3. Запускает его
```

```bash
npm test auth.test.ts

# Тест падает с той же ошибкой ✅
# Проблема подтверждена
```

## Шаг 4: Создание hotfix ветки

```
> Создай hotfix ветку от production

Claude выполняет:
git checkout production
git pull origin production
git checkout -b hotfix/jwt-validation-fix
```

## Шаг 5: Исправление

```
> Исправь проблему в @src/auth.ts
> Добавь algorithms: ['HS256'] к jwt.verify

Claude:
1. Читает auth.ts:45
2. Находит вызов jwt.verify
3. Добавляет options с algorithms
4. Обновляет все вызовы jwt.verify в файле
```

### До:
```typescript
const decoded = jwt.verify(token, SECRET_KEY);
```

### После:
```typescript
const decoded = jwt.verify(token, SECRET_KEY, {
  algorithms: ['HS256']
});
```

## Шаг 6: Добавление теста для регрессии

```
> Добавь тест который проверяет что это не повторится

Claude добавляет в auth.test.ts:
```typescript
describe('JWT validation', () => {
  it('should verify token with correct algorithm', () => {
    const token = generateToken({ userId: '123' });
    const decoded = verifyToken(token);
    expect(decoded.userId).toBe('123');
  });

  it('should reject token without algorithm specification', () => {
    // Ensure we're using algorithms option
    const token = jwt.sign({ userId: '123' }, SECRET_KEY);
    expect(() => verifyToken(token)).not.toThrow();
  });
});
```

## Шаг 7: Запуск тестов

```
> Запусти все auth тесты

Claude выполняет:
npm test -- auth.test.ts
```

**Результат:**
```
✅ All tests passed (12/12)
✅ JWT validation tests passed
```

## Шаг 8: Запуск всех тестов

```
> Запусти все тесты проекта

Claude выполняет:
npm test
```

**Результат:**
```
✅ All tests passed (247/247)
```

## Шаг 9: Проверка TypeScript и линтинг

```
> Проверь типы и линтинг

Claude выполняет:
npm run type-check && npm run lint
```

**Результат:**
```
✅ No type errors
✅ No lint errors
```

## Шаг 10: Ручное тестирование

```
> Запусти dev сервер и проверь вход

Claude выполняет:
npm run dev
```

Тестируете вручную:
1. Открываете страницу логина
2. Вводите credentials
3. ✅ Успешный вход
4. ✅ Token валидируется
5. ✅ Все работает

## Шаг 11: Коммит исправления

```
> Закоммить исправление

Claude выполняет:
git add src/auth.ts tests/auth.test.ts
git commit -m "fix(auth): add algorithms option to JWT verification

- Add algorithms: ['HS256'] to jwt.verify calls
- Required for jsonwebtoken v9.0.0 compatibility
- Add regression test

Fixes critical login issue affecting all users.

Breaking change in jsonwebtoken v9.0.0 requires
explicit algorithm specification.

Closes #789"
```

## Шаг 12: Запуш и создание hotfix PR

```
> Запуш ветку и создай urgent PR для hotfix

Claude выполняет:
git push -u origin hotfix/jwt-validation-fix

gh pr create \
  --base production \
  --title "🚨 HOTFIX: Fix JWT validation for login" \
  --body "## Critical Bug Fix

**Issue:** Users unable to login after jsonwebtoken upgrade

**Root Cause:** v9.0.0 requires explicit algorithms option

**Fix:** Added algorithms: ['HS256'] to jwt.verify

**Testing:**
- ✅ All tests pass
- ✅ Manual testing confirmed
- ✅ No type errors
- ✅ Regression test added

**Deployment:** Requires immediate deploy to production

**Rollback plan:** Revert to previous version if issues persist"
```

## Шаг 13: CI/CD проверки

GitHub Actions запускает:
- ✅ Tests (247/247 passed)
- ✅ TypeScript check
- ✅ Lint check
- ✅ Build successful

**Время:** ~2 минуты

## Шаг 14: Экстренный деплой

После быстрого code review:

```
> Смердж PR и задеплой на production

Claude выполняет:
gh pr merge --admin --squash
# Триггерит автоматический деплой
```

**Деплой занимает:** ~3-5 минут

## Шаг 15: Мониторинг после деплоя

```
> Проверь логи и Sentry после деплоя

Claude помогает мониторить:
- Смотрит логи deployment
- Проверяет Sentry на новые ошибки
- Мониторит метрики входов
```

**Результат через 5 минут:**
- ✅ Нет новых ошибок в Sentry
- ✅ Пользователи успешно входят
- ✅ Метрики нормализовались

## Шаг 16: Уведомление команды

```
> Напиши сообщение для Slack об исправлении

Claude генерирует:
```
🚨 RESOLVED: Login Issue

The critical login bug has been fixed and deployed to production.

**Timeline:**
- 14:30 - Issue reported
- 14:35 - Root cause identified
- 14:45 - Fix implemented and tested
- 14:50 - Deployed to production
- 14:55 - Confirmed resolved

**Root Cause:**
Breaking change in jsonwebtoken v9.0.0 library

**Fix:**
Added required algorithms option to JWT verification

**Prevention:**
- Regression test added
- Update dependency upgrade checklist
```

## Шаг 17: Post-mortem документация

```
> Создай post-mortem документ для этого инцидента

Claude создает файл docs/post-mortems/2025-11-30-login-failure.md:
```markdown
# Post-Mortem: Login Failure (2025-11-30)

## Summary
Critical authentication bug prevented all users from logging in.
Duration: 30 minutes. Affected: 100% of users.

## Timeline
- 14:00 - Deploy with jsonwebtoken v9.0.0 upgrade
- 14:30 - First user reports received
- 14:35 - Incident confirmed, root cause identified
- 14:45 - Fix implemented and tested
- 14:50 - Hotfix deployed to production
- 14:55 - Issue confirmed resolved

## Root Cause
jsonwebtoken v9.0.0 introduced breaking change requiring
explicit algorithms specification in jwt.verify().

## Resolution
Added algorithms: ['HS256'] option to all jwt.verify() calls.

## Impact
- Users affected: ~5,000 (100%)
- Duration: 30 minutes
- Failed login attempts: ~500

## Prevention
1. ✅ Added regression test
2. [ ] Update dependency upgrade checklist
3. [ ] Add integration tests for auth flow
4. [ ] Consider canary deployments for deps
5. [ ] Review breaking changes before upgrade

## Action Items
- [ ] #790 - Improve dependency upgrade process
- [ ] #791 - Add auth integration tests
- [ ] #792 - Setup canary deployments
```

## Итог

### Метрики
- **Время обнаружения:** 0 минут (пользователи сразу сообщили)
- **Время анализа:** 5 минут
- **Время исправления:** 10 минут
- **Время тестирования:** 5 минут
- **Время деплоя:** 5 минут
- **Общее время разрешения:** 25 минут

### Что помог сделать Claude Code
1. ✅ Быстро проанализировал причину
2. ✅ Нашел breaking change в библиотеке
3. ✅ Исправил код
4. ✅ Добавил тесты регрессии
5. ✅ Провел полное тестирование
6. ✅ Создал PR с описанием
7. ✅ Помог с post-mortem

### Без Claude Code
- Время поиска breaking change: ~15-30 мин
- Общее время: ~45-60 минут
- Больше стресса для команды

---

## Checklist для быстрого hotfix

### 1. Сбор информации ✅
- [ ] Симптомы проблемы
- [ ] Когда началось
- [ ] Сколько пользователей затронуто
- [ ] Логи/трейсы ошибок
- [ ] Что изменилось недавно

### 2. Анализ с Claude ✅
```
> Проанализируй этот баг: [описание + логи]
```

### 3. Воспроизведение ✅
```
> Воспроизведи проблему локально
```

### 4. Исправление ✅
```
> Исправь [конкретная проблема]
```

### 5. Тестирование ✅
```
> Запусти все тесты
> Добавь regression тест
```

### 6. Деплой ✅
```
> Создай hotfix PR
```

### 7. Мониторинг ✅
- Проверить логи
- Проверить метрики
- Убедиться что проблема решена

### 8. Документация ✅
```
> Создай post-mortem документ
```

---

## Советы для критических багов

### 1. Не паникуйте
Claude Code помогает действовать методично даже под давлением.

### 2. Предоставляйте контекст
Чем больше информации, тем точнее анализ:
- Логи ошибок
- Шаги воспроизведения
- Когда началось
- Что менялось

### 3. Тестируйте тщательно
Даже для hotfix:
- Запускайте все тесты
- Проверяйте вручную
- Добавляйте regression тесты

### 4. Коммуникация важна
- Уведомляйте команду
- Обновляйте статус
- Документируйте решение

### 5. Учитесь на ошибках
Создавайте post-mortem и action items для предотвращения повторения.
