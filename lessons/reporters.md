# Отчеты о тестировании (Reporters)

## Содержание
- [Что такое репортер и зачем он нужен?](#что-такое-репортер-и-зачем-он-нужен)
- [Обзор популярных репортеров для Jest](#обзор-популярных-репортеров-для-jest)
- [Настройка `jest-html-reporters`](#настройка-jest-html-reporters)
- [Настройка `allure-jest`](#настройка-allure-jest)
- [Настройка репортера для GitHub Actions](#настройка-репортера-для-github-actions)
- [Полезные ресурсы](#полезные-ресурсы)

## Что такое репортер и зачем он нужен?

**Репортер** в тестировании — это инструмент, который собирает информацию о выполнении тестов и представляет ее в наглядном, структурированном виде. Это может быть HTML-страница, XML-файл или интеграция с CI/CD системой.

**Основные задачи репортеров:**
- **Упрощение анализа**: Предоставляют читаемые отчеты, которые легко анализировать.
- **Повышение прозрачности**: Позволяют всей команде отслеживать статус выполнения тестов и выявлять проблемные зоны.
- **Облегчение отладки**: Часто содержат детальную информацию об ошибках, включая логи и скриншоты, что ускоряет исправление дефектов.

## Обзор популярных репортеров для Jest

- **Allure Report**: Мощная система для создания многофункциональных и интерактивных отчетов. Отлично подходит для TestOps.
- **Jest HTML Reporter**: Генерирует простой и понятный HTML-отчет. Хороший выбор для старта.
- **Mochawesome**: Популярный репортер, изначально созданный для фреймворка Mocha, но адаптированный и для Jest.
- **Jest JUnit**: Генерирует отчеты в формате JUnit XML, который поддерживается большинством CI/CD систем (Jenkins, TeamCity, GitLab CI).
- **GitHub Actions Reporter**: Встроенный в Jest репортер, который выводит аннотации об ошибках непосредственно в интерфейсе GitHub Actions.

## Настройка `jest-html-reporters`

Простой репортер, генерирующий локальный HTML-отчет.

### Установка
```bash
npm install jest-html-reporters --save-dev
```

### Конфигурация
В файле `jest.config.js` необходимо добавить репортер в массив `reporters`.

```javascript
// jest.config.js
/** @type {import('jest').Config} */
const config = {
  // ...
  reporters: [
    'default', // Оставляет стандартный вывод в консоль
    ['jest-html-reporters', {
      publicPath: './reports/html-report',
      filename: 'report.html',
      // Не открывать отчет автоматически в CI-окружении
      openReport: !process.env.CI
    }]
  ],
};
```
> **Важно**: Не забудьте добавить папку с отчетами (например, `reports/`) в файл `.gitignore`.

### Добавление информации в отчет
Репортер позволяет добавлять в отчет дополнительный контекст прямо из тестов.

```javascript
import { addMsg } from 'jest-html-reporters/helper';

it('Успешная авторизация', async () => {
  const credentials = {
    userName: config.username,
    password: config.password,
  };
  // JSON.stringify используется для красивого форматирования объекта
  addMsg({ message: `Доступы: ${JSON.stringify(credentials, null, 2)}` });

  const response = await AuthService.generateTokenCached(credentials);
  // ...
});
```

Для прикрепления файлов можно использовать функцию `addAttach`.

## Настройка `allure-jest`

Allure — это мощный мультиязычный инструмент для создания отчетов, который отлично подходит для построения процессов TestOps.

### Предварительные требования
- **Java**: Убедитесь, что установлена Java версии 8 или выше.
- **Allure Commandline**: Инструмент для генерации отчетов.

```bash
# Установка Allure Commandline
npm install --save-dev allure-commandline
```

### Установка плагина для Jest
```bash
npm install --save-dev allure-jest
```

### Конфигурация
В `jest.config.js` необходимо указать `testEnvironment`.

```javascript
// jest.config.js
const config = {
  testEnvironment: 'allure-jest/node',
  testEnvironmentOptions: {
    resultsDir: 'reports/allure-results'
  }
};
```
> **Важно**: Добавьте папки `allure-results` и `allure-report` в `.gitignore`.

### Использование
Для удобства добавьте команды в секцию `scripts` вашего `package.json`.

```json
{
  "scripts": {
    "test": "jest",
    "allure:generate": "allure generate reports/allure-results --clean",
    "allure:open": "allure open reports/allure-report",
    "allure:serve": "allure serve reports/allure-results"
  }
}
```

**Порядок работы:**
1.  `npm test` — запуск тестов, генерирует JSON-файлы в `allure-results`.
2.  `npm run allure:generate` — генерирует HTML-отчет в папку `allure-report`.
3.  `npm run allure:open` — открывает сгенерированный HTML-отчет в браузере.

### История тестов (Test History)
Allure может отслеживать историю прогонов тестов, показывая тренды и помогая выявлять нестабильные ("flaky") тесты. Для этого необходимо перед генерацией нового отчета копировать директорию `history` из предыдущего отчета (`allure-report/history`) в директорию с новыми результатами (`allure-results/history`).

## Настройка репортера для GitHub Actions

Jest имеет встроенный репортер, который улучшает отображение результатов тестов в логах GitHub Actions, добавляя аннотации к упавшим тестам.

### Конфигурация
Репортер следует включать только при запуске в CI-окружении. Это можно сделать, проверив наличие переменной окружения `process.env.CI`.

```javascript
// jest.config.js
const config = {
  reporters: ['default'],
  // ...
};

// Добавляем репортер только в CI
if (process.env.CI) {
  config.reporters.push(['github-actions', { silent: false }]);
}
```

## Полезные ресурсы
- **Allure Report**: [Официальный сайт](https://allurereport.org/)
- **Awesome Jest**: [Подборка полезных утилит и репортеров для Jest](https://github.com/jest-community/awesome-jest/blob/main/README.md#reporters)
- **Jest Configuration**: [Официальная документация по настройке Jest](https://jestjs.io/docs/configuration)