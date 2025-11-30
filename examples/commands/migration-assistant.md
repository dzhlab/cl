---
description: Помощь в миграции кода на новую версию библиотеки/фреймворка
argument-hint: [старая версия] [новая версия] [библиотека]
---

Помоги мигрировать с $1 на $2 для $3

## Процесс миграции:

### 1. Анализ текущего состояния

**Что проверить:**
- Текущая версия установленной библиотеки
- Где используется библиотека в проекте
- Зависимые пакеты
- Тесты покрывающие использование

```bash
# Проверка текущей версии
npm list $3
# или
pip show $3

# Найти все использования
grep -r "import.*$3" .
# или для JS
rg "from '$3'" --type ts
```

### 2. Изучение breaking changes

**Источники информации:**
- CHANGELOG.md библиотеки
- Migration guide (если есть)
- GitHub releases
- Breaking changes в документации

**Что искать:**
- Переименованные функции/методы
- Изменения в API
- Удалённые функции
- Новые обязательные параметры
- Изменения в поведении

### 3. Создание плана миграции

```markdown
## План миграции $3: $1 → $2

### Breaking Changes
1. `oldFunction()` → `newFunction()`
2. `Config.option` → `Config.newOption`
3. Removed: `deprecatedMethod()`

### Файлы для изменения
- [ ] src/api/users.ts
- [ ] src/services/auth.ts
- [ ] tests/api.test.ts

### Шаги
1. Обновить package.json
2. Изменить импорты
3. Обновить вызовы функций
4. Обновить конфигурацию
5. Запустить и исправить тесты
6. Обновить документацию
```

## Типичные миграции:

### React 17 → 18

**Breaking changes:**
```javascript
// ❌ React 17
import ReactDOM from 'react-dom';
ReactDOM.render(<App />, document.getElementById('root'));

// ✅ React 18
import { createRoot } from 'react-dom/client';
const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

**Автоматическая миграция:**
```bash
npx react-codemod update-react-imports
```

### Vue 2 → 3

**Breaking changes:**
```javascript
// ❌ Vue 2
new Vue({
  el: '#app',
  render: h => h(App)
});

// ✅ Vue 3
import { createApp } from 'vue';
createApp(App).mount('#app');
```

### Express 4 → 5

**Breaking changes:**
```javascript
// ❌ Express 4
app.get('/user/:id', (req, res, next) => {
  // callback-based
  User.findById(req.params.id, (err, user) => {
    if (err) return next(err);
    res.json(user);
  });
});

// ✅ Express 5 (async/await support)
app.get('/user/:id', async (req, res) => {
  const user = await User.findById(req.params.id);
  res.json(user);
});
```

### Mongoose 6 → 7

**Breaking changes:**
```javascript
// ❌ Mongoose 6
mongoose.connect(uri, {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

// ✅ Mongoose 7 (options removed as defaults)
mongoose.connect(uri);
```

### Jest 27 → 29

**Breaking changes:**
```javascript
// ❌ Jest 27 (default environment: 'jsdom')
// package.json
{
  "jest": {
    "testEnvironment": "node" // если нужен node
  }
}

// ✅ Jest 29 (default: 'node')
{
  "jest": {
    "testEnvironment": "jsdom" // если нужен jsdom
  }
}
```

## Пошаговый процесс:

### Шаг 1: Создать ветку для миграции

```bash
git checkout -b migration/$3-$1-to-$2
```

### Шаг 2: Обновить зависимость

```bash
# npm
npm install $3@$2

# yarn
yarn upgrade $3@$2

# pnpm
pnpm update $3@$2
```

### Шаг 3: Найти все использования

```bash
# Где импортируется
rg "from '$3'" --type ts --type js

# Где используется
rg "$3\." --type ts --type js
```

### Шаг 4: Применить изменения

Для каждого файла:

1. **Обновить импорты**
```typescript
// До
import { OldAPI } from '$3';

// После
import { NewAPI } from '$3';
```

2. **Обновить вызовы**
```typescript
// До
const result = OldAPI.doSomething(param);

// После
const result = await NewAPI.doSomething({ param });
```

3. **Обновить типы**
```typescript
// До
type User = OldAPI.User;

// После
type User = NewAPI.UserType;
```

### Шаг 5: Обновить тесты

```bash
npm test
```

**Типичные проблемы:**
- Изменились моки
- Новые методы требуют других аргументов
- Async функции требуют await

```typescript
// До
jest.mock('$3', () => ({
  OldAPI: {
    doSomething: jest.fn()
  }
}));

// После
jest.mock('$3', () => ({
  NewAPI: {
    doSomething: jest.fn()
  }
}));
```

### Шаг 6: Проверить TypeScript типы

```bash
npx tsc --noEmit
```

Исправить ошибки типизации.

### Шаг 7: Запустить линтер

```bash
npm run lint
```

### Шаг 8: Обновить документацию

- README.md
- CHANGELOG.md
- Комментарии в коде
- API документация

### Шаг 9: Коммит и PR

```bash
git add .
git commit -m "chore: migrate $3 from $1 to $2

Breaking changes:
- Updated API calls to new format
- Replaced deprecated methods
- Updated types

All tests passing.
Closes #XXX"

git push origin migration/$3-$1-to-$2
gh pr create
```

## Инструменты для миграции:

### Codemod
Автоматические преобразования кода:

```bash
# React
npx react-codemod

# Next.js
npx @next/codemod

# ESLint автофиксы
npx eslint --fix .
```

### jscodeshift
Создание собственных трансформаций:

```bash
npm install -g jscodeshift

# Запуск трансформации
jscodeshift -t transform.js src/
```

## Чеклист миграции:

### Перед началом
- [ ] Прочитать CHANGELOG и migration guide
- [ ] Создать резервную ветку
- [ ] Убедиться что все тесты проходят
- [ ] Уведомить команду о начале миграции

### Во время миграции
- [ ] Обновить зависимость
- [ ] Найти все использования
- [ ] Применить breaking changes
- [ ] Обновить тесты
- [ ] Проверить типы TypeScript
- [ ] Запустить линтер
- [ ] Проверить работу вручную

### После миграции
- [ ] Все тесты проходят
- [ ] Нет ошибок TypeScript
- [ ] Нет lint ошибок
- [ ] Документация обновлена
- [ ] PR создан и проверен
- [ ] Team уведомлена

## Откат в случае проблем:

```bash
# Откатить изменения
git reset --hard origin/main

# Или вернуть старую версию
npm install $3@$1
```

## Советы:

### 1. Делайте поэтапно
Не обновляйте сразу с версии 1 на версию 10.
Идите через промежуточные: 1 → 5 → 10

### 2. Читайте migration guides
Часто есть официальные руководства по миграции.

### 3. Ищите codemods
Многие популярные библиотеки предоставляют автоматические скрипты миграции.

### 4. Тестируйте тщательно
Запускайте полный набор тестов, включая E2E.

### 5. Мигрируйте зависимости постепенно
Не обновляйте всё сразу. По одной библиотеке за раз.

### 6. Проверяйте peer dependencies
Убедитесь что обновление не конфликтует с другими пакетами:

```bash
npm ls
# Проверить warnings о peer dependencies
```

## Что делать:

1. Прочитай CHANGELOG для $3 между версиями $1 и $2
2. Найди все использования $3 в проекте
3. Определи breaking changes которые нас затрагивают
4. Создай детальный план миграции
5. Предложи изменения для каждого файла
6. Обнови тесты
7. Создай чеклист для проверки
