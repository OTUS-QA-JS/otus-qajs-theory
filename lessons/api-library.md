# Библиотеки для тестирования API и построение тестового фреймворка

## Содержание
- [Подготовка тестового окружения](#подготовка-тестового-окружения)
- [Обзор HTTP-клиентов](#обзор-http-клиентов)
- [Построение слоя сервисов](#построение-слоя-сервисов)
- [Централизация импортов (Re-exports)](#централизация-импортов-re-exports)
- [Полезные ресурсы](#полезные-ресурсы)

## Подготовка тестового окружения

Перед написанием тестов необходимо настроить окружение, включая конфигурацию и тестовые данные (фикстуры).

### Конфигурация
**Конфиг** — это объект или файл с данными, которые зависят от окружения (например, URL стенда, учетные данные). Эти данные не должны меняться во время выполнения тестов.

Для управления конфигурацией удобно использовать переменные окружения и пакет `dotenv`.

**Пример конфигурационного файла:**
```javascript
// framework/config/config.js
import 'dotenv/config';

const config = {
  // Оператор ?? (nullish coalescing) устанавливает значение по умолчанию,
  // если переменная окружения равна null или undefined.
  baseURL: process.env.TEST_API_URL ?? 'https://api.bookstore.com',
  username: process.env.TEST_USERNAME ?? 'default-user',
  password: process.env.TEST_PASSWORD ?? 'default-pass',
};

// Object.freeze() делает объект неизменяемым на первом уровне вложенности.
export default Object.freeze(config);
```

Чтобы сделать объект полностью неизменяемым (включая вложенные объекты), потребуется функция "глубокой заморозки".

### Фикстуры (Test Data)
Фикстуры — это тестовые данные, которые используются в тестах. Они бывают двух типов:

- **Статические**: Заранее подготовленные данные, например, JSON-файл с ожидаемым ответом от API.
- **Динамические**: Данные, которые генерируются во время выполнения тестов. Для этого удобно использовать библиотеку `@faker-js/faker`.

**Пример генерации динамических данных:**
```javascript
// framework/fixtures/user.fixture.js
import { faker } from '@faker-js/faker';

export function generateUserCredentials() {
  return {
    username: faker.internet.email(),
    password: faker.internet.password(),
  };
}
```

## Обзор HTTP-клиентов

Для отправки запросов к API в Node.js существует несколько популярных библиотек.

### Нативный Fetch API
Встроенный в Node.js (с версии 18) инструмент для выполнения HTTP-запросов.
- **Плюсы**: Не требует установки зависимостей, дает полный контроль над запросом.
- **Минусы**: Более многословный синтаксис, требует ручной обработки данных (например, вызова `.json()`).

```javascript
const response = await fetch(url, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ userName, password }),
});
const data = await response.json();
```

### Supertest
Популярная библиотека, особенно удобная для тестирования Node.js-приложений (Express, Koa). Обладает "текучим" (fluent) API.

```javascript
import supertest from 'supertest';

const response = await supertest(config.baseURL)
  .post('/Account/v1/GenerateToken')
  .send({ userName, password });

// Данные доступны в response.body
console.log(response.body);
```

### Got
Мощная и современная ESM-библиотека для выполнения HTTP-запросов.

```javascript
import got from 'got';

const response = await got.post(url, {
  json: { userName, password },
  responseType: 'json',
});

// Данные доступны в response.body
console.log(response.body);
```

### Axios
Одна из самых популярных библиотек. Проста в использовании, имеет богатый функционал, например, создание предварительно настроенных клиентов.

```javascript
import axios from 'axios';

const response = await axios.post(url, { userName, password });

// Данные доступны в response.data
console.log(response.data);
```

## Построение слоя сервисов

Чтобы сделать тесты более читаемыми и не зависящими от конкретной HTTP-библиотеки, создается **слой сервисов**. Каждый сервис инкапсулирует логику взаимодействия с определенной группой эндпоинтов (например, `AuthService`, `BookService`).

### Создание HTTP-клиента (на примере Axios)
Можно создать один настроенный экземпляр клиента и переиспользовать его во всех сервисах.

```javascript
// framework/services/client.js
import axios from 'axios';
import config from '../config/config';

const client = axios.create({
  baseURL: config.baseURL,
  // Отключаем стандартную проверку статуса, чтобы обрабатывать 4xx/5xx ответы в тестах
  validateStatus: () => true,
});

export default client;
```

### Унификация ответов (Паттерн "Адаптер")
Разные библиотеки возвращают данные и статус в разных полях (`.data` у axios, `.body` у supertest, `.json()` у fetch). Слой сервисов должен **адаптировать** эти ответы к единому формату, чтобы тесты всегда работали с одинаковой структурой.

**Пример реализации сервиса с унификацией ответа:**

```javascript
// framework/services/AuthService.js
import client from './client'; // Наш настроенный axios-клиент

async function generateToken({ userName, password }) {
  const response = await client.post('/Account/v1/GenerateToken', { userName, password });

  // Приводим ответ к единому формату { status, data, headers }
  return {
    status: response.status,
    data: response.data, // axios возвращает данные в .data
    headers: response.headers,
  };
}

export default { generateToken };
```

Теперь, если мы решим заменить `axios` на `supertest`, нам нужно будет изменить только код внутри сервиса, а тесты останутся прежними.

## Централизация импортов (Re-exports)

Чтобы не писать в тестах длинные пути (`import AuthService from '../../framework/services/AuthService.js'`), используется паттерн **"Компоновщик"** с помощью "barrel files" (`index.js`). Они собирают все экспорты из директории в одном месте.

**Структура проекта:**
```
framework/
├── config/
│   └── config.js
├── services/
│   ├── AuthService.js
│   ├── BookService.js
│   └── index.js       // Barrel file для сервисов
└── index.js           // Главный barrel file фреймворка
```

**Пример `framework/services/index.js`:**
```javascript
export { default as AuthService } from './AuthService';
export { default as BookService } from './BookService';
```

**Пример `framework/index.js`:**
```javascript
export { default as config } from './config/config';
export * from './services'; // Ре-экспортируем все из services/index.js
```

**Результат**: импорты в тестах становятся короткими и чистыми.
```javascript
// Раньше:
// import config from '../../framework/config/config';
// import AuthService from '../../framework/services/AuthService';

// Теперь:
import { config, AuthService } from '../../framework';
```

## Полезные ресурсы
- **PactumJS**: [Инструмент для тестирования REST API](https://pactumjs.github.io/)
