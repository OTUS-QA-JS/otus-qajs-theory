# Основные сценарии использования Playwright

## Содержание
- [Введение в Playwright](#введение-в-playwright)
- [Преимущества Playwright](#преимущества-playwright)
- [Основные возможности](#основные-возможности)
- [Архитектура Playwright](#архитектура-playwright)
- [Установка и настройка](#установка-и-настройка)
- [Первый тест и его разбор](#первый-тест-и-его-разбор)
- [Инструменты для отладки](#инструменты-для-отладки)
- [Лучшие практики](#лучшие-практики)
- [Практические упражнения](#практические-упражнения)
- [Полезные ресурсы](#полезные-ресурсы)

## Введение в Playwright

**Playwright** — это современная библиотека для автоматизации браузеров, разработанная Microsoft. Она предназначена для тестирования веб-приложений с использованием современных API и протоколов.

### Что такое Playwright?

Playwright представляет собой инструмент нового поколения для автоматизации веб-браузеров, который решает многие проблемы традиционных решений для тестирования UI:

- **Современная архитектура**: Использует современные протоколы для взаимодействия с браузерами
- **Мультибраузерность**: Нативная поддержка Chromium, Firefox и WebKit (Safari)
- **Стабильность**: Надежное выполнение тестов благодаря встроенным ожиданиям
- **Простота использования**: Интуитивно понятный API для написания тестов

## Преимущества Playwright

### Ключевые преимущества

**Мультибраузерность**: Поддержка Chromium, Firefox и WebKit (Safari) позволяет тестировать приложение на разных платформах без дополнительной настройки.

**Стабильность**: Надежное выполнение тестов за счет работы с современными протоколами (WebSocket, DevTools Protocol).

**Современные API**: Интуитивно понятный и расширяемый API для простоты разработки тестов.

### Сравнение с другими инструментами

**Selenium**: Более устаревшая архитектура, требует отдельной настройки драйверов, более высокий порог входа.

**Cypress**: Предлагает «всё в одном», но имеет ограничения при работе с мультибраузерностью и динамическими элементами.

**Playwright объединяет преимущества обоих**, предлагая гибкость и стабильность с современными возможностями.

## Основные возможности

### Поддержка браузеров
- **Chromium, Firefox, WebKit (Safari)**: Тестирование на различных движках без дополнительной настройки

### Автоматизация действий
- Навигация по URL, клики, ввод текста, взаимодействие с формами – все стандартные операции автоматизируются через единый API

### Перехват сетевых запросов и эмуляция условий
- Возможность перехвата, изменения или блокировки сетевых запросов для тестирования отказоустойчивости
- Эмуляция условий, таких как медленная сеть или отсутствие интернет-соединения

### Встроенный тест-раннер (Playwright Test)
- Интегрированное средство запуска тестов с отчетностью и удобными инструментами отладки

### Пример базового синтаксиса

```javascript
import { test, expect } from '@playwright/test';

test('Locators example', async ({ page }) => {
  await page.goto('');
  
  // Использование CSS-селекторов по умолчанию
  const inputBox = page.locator('input.new-todo');
  const todoList = page.locator('.todo-list');
  
  await inputBox.fill('Learn Playwright');
  await inputBox.press('Enter');
  await expect(todoList).toHaveText('Learn Playwright');
});
```

*Примечание: PW использует CSS-селекторы по умолчанию*

## Архитектура Playwright

### Современная архитектура

**Легкая интеграция**: Работает через языковые библиотеки (JavaScript/TypeScript, Python, Java, C#).

**Использование современных протоколов**: Взаимодействие с браузерами происходит через WebSocket и DevTools Protocol, что обеспечивает быструю передачу команд и обратную связь.

**Встроенные возможности для ожиданий и отладки**: 
- Автоматические ожидания (auto-wait) для асинхронных операций, что снижает необходимость в ручном управлении паузами
- Отладочные инструменты, позволяющие поставить тест на паузу в точке для детального анализа

## Установка и настройка

### Быстрая установка через npm

```bash
npm init playwright@latest
```

Эта команда создаёт базовую структуру проекта, демонстрационные тесты и устанавливает зависимости.

### Структура проекта

После установки вы получите:
- Файл конфигурации (`playwright.config.js`), который задаёт настройки запуска тестов
- Папку с тестами, где хранятся все тестовые сценарии

### Базовая настройка и запуск тестов

```bash
# Простой старт: запуск тестов
npx playwright test

# Запуск с отчетом
npx playwright test --reporter=html

# Запуск в режиме отладки
npx playwright test --debug
```

**Совет**: Вся информация по Playwright хорошо описана в официальной документации [playwright.dev](https://playwright.dev)

## Первый тест и его разбор

### Демонстрационные тесты

При инициализации проекта Playwright создает два примера тестов:

#### Первый тест "has title"
```javascript
// @ts-check
import { test, expect } from '@playwright/test';

test('has title', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  
  // Expect a title "to contain" a substring.
  await expect(page).toHaveTitle(/Playwright/);
});
```

**Что происходит**:
- Открывается страница https://playwright.dev/
- Проверяется, что заголовок страницы содержит слово "Playwright"

#### Второй тест "get started link"
```javascript
test('get started link', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  
  // Click the get started link.
  await page.getByRole('link', { name: 'Get started' }).click();
  
  // Expects page to have a heading with the name of Installation.
  await expect(page.getByRole('heading', { name: 'Installation' })).toBeVisible();
});
```

**Что происходит**:
- Открывается страница https://playwright.dev/
- На странице находится ссылка с текстом "Get started" и по ней выполняется клик
- После клика ожидается, что на новой странице появится заголовок "Installation"

### Создание собственных тестов

Теперь, когда мы разобрали готовые примеры, давайте создадим собственные тесты:

```javascript
// tests/specs/my-first-test.spec.js
import { test, expect } from '@playwright/test';

test('мой первый тест', async ({ page }) => {
  // Переходим на страницу
  await page.goto('https://playwright.dev/');
  
  // Проверяем заголовок
  const title = await page.title();
  expect(title).toContain('Playwright');
  
  // Проверяем наличие текста на странице
  await expect(page.getByText('Playwright enables reliable end-to-end testing')).toBeVisible();
});

test('взаимодействие с элементами', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  
  // Кликаем по ссылке
  await page.getByRole('link', { name: 'Get started' }).click();
  
  // Проверяем, что перешли на нужную страницу
  await expect(page.getByRole('heading', { name: 'Installation' })).toBeVisible();
  
  // Проверяем URL
  expect(page.url()).toContain('/docs/intro');
});
```

## Инструменты для отладки

### Дебаг с помощью встроенных инструментов

Playwright предоставляет несколько мощных инструментов для отладки:

#### 1. Debug режим

```bash
npx playwright test --debug
```

Этот режим открывает Playwright Inspector - графический интерфейс для пошаговой отладки тестов.

#### 2. Trace Viewer

В конфиге прописываем:
```javascript
// playwright.config.js
module.exports = {
  use: {
    trace: 'retain-on-failure', // Создает trace-файл при падении теста
  },
};
```

Для просмотра trace:
```bash
npx playwright show-trace test-results/example-get-started-link-webkit/trace.zip
```

#### 3. Дебаг в IDE

Playwright поддерживает стандартные breakpoint в популярных IDE:
- VS Code с расширением Playwright Test
- WebStorm/IntelliJ IDEA
- Любая IDE с поддержкой Node.js debugging

### Практические советы по отладке

```javascript
test('отладка теста', async ({ page }) => {
  await page.goto('https://example.com');
  
  // Пауза для ручного исследования
  await page.pause();
  
  // Скриншот для анализа
  await page.screenshot({ path: 'debug-screenshot.png' });
  
  // Логирование состояния элемента
  const element = page.locator('#my-element');
  console.log('Element visible:', await element.isVisible());
  console.log('Element text:', await element.textContent());
});
```

## Лучшие практики

### Разделение окружений
Если в вашем проекте разные конфигурации для разработки, тестирования и продакшена, используйте переменные окружения или несколько файлов конфигурации для гибкости.

### Документация
Задокументируйте ключевые параметры в файле конфигурации, чтобы новые участники команды быстро ориентировались в настройках.

### Отчётность
Настройте подробный формат отчетов (html, junit), чтобы легко анализировать результаты тестов. Это особенно полезно при интеграции с CI/CD.

### Чистая структура
Разделяйте тестовые файлы по логическим группам или функциональным областям. Это облегчит поддержку и масштабирование тестового набора.

### Пример организации проекта

```
tests/
├── pages/
│   ├── homePage.js
│   ├── searchPage.js
│   └── index.js
├── specs/
│   ├── smoke/
│   │   └── basic.spec.js
│   ├── regression/
│   │   └── full-flow.spec.js
│   └── api/
│       └── endpoints.spec.js
├── fixtures/
│   └── testData.js
└── utils/
    └── helpers.js
```

## Практические упражнения

### Упражнение 1: Базовый тест поиска

Создайте тест, который:
1. Установите Playwright: `npm init playwright@latest`
2. В файле `example.spec.js` напишите тест:
   - Открыть страницу https://playwright.dev/
   - Ввести в поле "Search" текст "LocatorAssertions"
   - Нажать кнопку "Enter"
   - Проверить что в title есть текст "LocatorAssertions | Playwright"

```javascript
// tests/specs/search.spec.js
import { test, expect } from '@playwright/test';

test('поиск по документации', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  
  // Найти поле поиска и ввести текст
  const searchInput = page.locator('[placeholder*="Search"]');
  await searchInput.fill('LocatorAssertions');
  await searchInput.press('Enter');
  
  // Проверить заголовок страницы
  await expect(page).toHaveTitle(/LocatorAssertions.*Playwright/);
});
```

### Упражнение 2: Тест с несколькими действиями

Создайте тест, который выполняет последовательность действий:

```javascript
// tests/specs/navigation.spec.js
import { test, expect } from '@playwright/test';

test('навигация по документации', async ({ page }) => {
  // Переходим на главную страницу
  await page.goto('https://playwright.dev/');
  
  // Кликаем по ссылке "Docs"
  await page.getByRole('link', { name: 'Docs' }).click();
  
  // Проверяем, что перешли на страницу документации
  await expect(page).toHaveURL(/.*docs.*/);
  
  // Проверяем наличие заголовка
  await expect(page.getByRole('heading', { name: 'Getting started' })).toBeVisible();
  
  // Кликаем по разделу "Writing tests"
  await page.getByRole('link', { name: 'Writing tests' }).click();
  
  // Проверяем, что контент загрузился
  await expect(page.getByText('Tests are written using test')).toBeVisible();
});
```

### Упражнение 3: Тест с заполнением формы

```javascript
// tests/specs/form.spec.js
import { test, expect } from '@playwright/test';

test('работа с формой', async ({ page }) => {
  // Переходим на страницу с примером формы
  await page.goto('https://the-internet.herokuapp.com/login');
  
  // Заполняем поля формы
  await page.locator('#username').fill('tomsmith');
  await page.locator('#password').fill('SuperSecretPassword!');
  
  // Нажимаем кнопку входа
  await page.locator('button[type="submit"]').click();
  
  // Проверяем успешный вход
  await expect(page.getByText('You logged into a secure area!')).toBeVisible();
  
  // Проверяем URL
  expect(page.url()).toContain('/secure');
  
  // Делаем скриншот результата
  await page.screenshot({ path: 'login-success.png' });
});
```

### Контрольные вопросы

1. **Как называется файл, в котором прописываются параметры для запуска тестов?**
   - Ответ: `playwright.config.js`

2. **Какие параметры можно там использовать?**
   - Ответ: `trace`, `headless`, `browser`, `timeout`, `reporter`, `workers` и многие другие

3. **Для чего нужно использовать await?**
   - Ответ: Для ожидания завершения асинхронных операций (промисов) перед продолжением выполнения кода

## Полезные ресурсы

- **Официальная документация**: [playwright.dev](https://playwright.dev)
- **GitHub репозиторий**: [microsoft/playwright](https://github.com/microsoft/playwright)
- **Примеры тестов**: [playwright-dev/playwright](https://github.com/playwright-dev/playwright/tree/main/tests)
- **Best Practices**: [Playwright Best Practices](https://playwright.dev/docs/best-practices)
- **API Reference**: [Playwright API](https://playwright.dev/docs/api/class-playwright)
- [Добавляем кнопку «Fix with AI» в отчёты Playwright](https://habr.com/ru/articles/875448/)

### Команды для справки

```bash
# Установка
npm init playwright@latest

# Запуск тестов
npx playwright test

# Запуск в режиме отладки
npx playwright test --debug

# Генерация отчета
npx playwright show-report

# Просмотр trace
npx playwright show-trace path-to-trace.zip

# Обновление браузеров
npx playwright install
```