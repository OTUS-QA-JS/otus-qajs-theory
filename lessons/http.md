# HTTP и тестирование API

## Содержание

- [Что такое API](#что-такое-api)
- [Виды тестирования API](#виды-тестирования-api)
- [Основы HTTP протокола](#основы-http-протокола)
- [Структура HTTP запроса](#структура-http-запроса)
- [Структура HTTP ответа](#структура-http-ответа)
- [HTTP методы](#http-методы)
- [Коды состояния HTTP](#коды-состояния-http)
- [REST API](#rest-api)
- [Авторизация и аутентификация](#авторизация-и-аутентификация)
- [Практическая работа с API](#практическая-работа-с-api)
- [Полезные ресурсы](#полезные-ресурсы)

## Что такое API

**API** (Application Programming Interface) - соглашение о том, как разные программы могут взаимодействовать друг с другом. API обеспечивает возможность обмена информацией, запроса данных и выполнения определенных задач между программами.

### Основные типы API

- **REST API** - архитектурный стиль для веб-сервисов
- **GraphQL** - язык запросов для API
- **gRPC** - высокопроизводительный RPC фреймворк
- **JSON:API** - спецификация для построения API в JSON
- **JSON-RPC** - удаленный вызов процедур
- **SOAP API** - протокол для обмена структурированными сообщениями
- **WebSocket** - протокол для двунаправленной связи

## Виды тестирования API

### Функциональное тестирование

**Тестирование методов**
Проверка корректной работы каждого метода API и соответствия ожидаемым результатам.

**Тестирование взаимодействий**
Проверка способности API взаимодействовать с другими API, компонентами и сервисами.

### Безопасность и доступ

**Тестирование авторизации и аутентификации**
Проверка механизмов доступа к API и определение прав пользователей.

**Тестирование безопасности**
Анализ уязвимостей, тесты на проникновение, проверка защиты данных.

### Надежность

**Тестирование обработки ошибок**
Проверка поведения API при некорректных данных и исключительных ситуациях.

**Тестирование производительности**
Анализ пропускной способности и поведения при повышенной нагрузке.

## Основы HTTP протокола

**HTTP** (HyperText Transfer Protocol) - протокол передачи гипертекста. Набор правил для обмена данными между клиентом и сервером в интернете.

### Особенности HTTP

- Текстовый протокол
- Модель запрос-ответ
- Без сохранения состояния (stateless)
- Поддержка различных типов данных

## Структура HTTP запроса

HTTP запрос состоит из трех основных частей:

### 1. Строка запроса (Request Line)

```
POST /api/users HTTP/1.1
```

Содержит:
- **HTTP метод** - действие для выполнения (GET, POST, DELETE и др.)
- **URI** - путь к ресурсу на сервере
- **Версия HTTP** - используемая версия протокола

### 2. Заголовки (Headers)

```
Host: api.example.com
Content-Type: application/json
Authorization: Bearer token123
Content-Length: 45
```

Дополнительная информация о запросе:
- **Host** - доменное имя сервера
- **Content-Type** - тип отправляемых данных
- **Authorization** - данные для авторизации
- **Content-Length** - размер тела запроса

### 3. Тело запроса (Body)

```json
{
  "name": "John Doe",
  "email": "john@example.com"
}
```

Данные, отправляемые серверу (только для некоторых методов).

## Структура HTTP ответа

### 1. Строка статуса (Status Line)

```
HTTP/1.1 200 OK
```

Содержит:
- **Версия HTTP**
- **Код статуса** - числовой код результата
- **Фраза статуса** - текстовое описание

### 2. Заголовки ответа

```
Content-Type: application/json
Content-Length: 156
Set-Cookie: sessionId=abc123
```

### 3. Тело ответа

```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
```

## HTTP методы

### Основные методы

- **GET** - получить ресурс с сервера
- **POST** - отправить данные для создания ресурса
- **PUT** - заменить ресурс целиком
- **PATCH** - частично обновить ресурс
- **DELETE** - удалить ресурс

### Дополнительные методы

- **HEAD** - получить заголовки без тела ответа
- **OPTIONS** - получить список доступных методов

## Коды состояния HTTP

### 2xx - Успешные запросы

- **200 OK** - запрос выполнен успешно
- **201 Created** - ресурс создан
- **204 No Content** - успешно, но нет содержимого

### 3xx - Перенаправления

- **301 Moved Permanently** - ресурс перемещен навсегда
- **302 Found** - временное перенаправление

### 4xx - Ошибки клиента

- **400 Bad Request** - некорректный запрос
- **401 Unauthorized** - требуется аутентификация
- **403 Forbidden** - доступ запрещен
- **404 Not Found** - ресурс не найден

### 5xx - Ошибки сервера

- **500 Internal Server Error** - внутренняя ошибка сервера
- **502 Bad Gateway** - некорректный ответ вышестоящего сервера
- **503 Service Unavailable** - сервис недоступен

## REST API

**REST** (Representational State Transfer) - архитектурный стиль для проектирования веб-сервисов, определенный Роем Филдингом в 2000 году.

### Принципы REST

- **Единообразный интерфейс** - стандартные HTTP методы
- **Без сохранения состояния** - каждый запрос независим
- **Кешируемость** - ответы могут кешироваться
- **Слоистая архитектура** - возможность промежуточных серверов
- **Клиент-сервер** - разделение ответственности

### CRUD операции

- **Create** - POST запрос для создания
- **Read** - GET запрос для получения
- **Update** - PUT/PATCH для обновления
- **Delete** - DELETE для удаления

## Авторизация и аутентификация

### Основные понятия

**Идентификация** - определение пользователя в системе по уникальному идентификатору.

**Аутентификация** - проверка подлинности пользователя (обычно по паролю или другому секрету).

**Авторизация** - предоставление прав доступа к ресурсам после успешной аутентификации.

### Типы авторизации в API

**Basic Authentication**
```javascript
const response = await fetch('https://api.example.com', {
  headers: {
    'Authorization': `Basic ${btoa('username:password')}`
  }
})
```

**Bearer Token (JWT)**
```javascript
const response = await fetch('https://api.example.com', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
})
```

**API Key**
```javascript
const response = await fetch('https://api.example.com', {
  headers: {
    'X-API-Key': 'your-api-key'
  }
})
```

## Практическая работа с API

### Fetch API в JavaScript

**GET запрос**
```javascript
const response = await fetch('https://reqres.in/api/users')
const data = await response.json()
console.log('Status:', response.status)
console.log('Data:', data)
```

**POST запрос**
```javascript
const response = await fetch('https://reqres.in/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: 'John',
    job: 'Developer'
  })
})
```

**DELETE запрос**
```javascript
const response = await fetch('https://reqres.in/api/users/2', {
  method: 'DELETE'
})
// Внимание: response.json() может вызвать ошибку для пустого ответа
```

### Обработка ошибок

```javascript
try {
  const response = await fetch('https://api.example.com/data')
  
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`)
  }
  
  const data = await response.json()
  console.log(data)
} catch (error) {
  console.error('Fetch error:', error.message)
}
```

### Пример теста API

```javascript
describe('User API', () => {
  it('should create user successfully', async () => {
    const response = await fetch('https://reqres.in/api/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        name: 'John Doe',
        job: 'QA Engineer'
      })
    })
    
    const data = await response.json()
    
    expect(response.status).toBe(201)
    expect(data.name).toBe('John Doe')
    expect(data.id).toBeDefined()
  })
})
```

## Полезные ресурсы

### Инструменты для тестирования API

- [Postman](https://www.postman.com/) - популярная платформа для работы с API
- [Insomnia](https://insomnia.rest/) - альтернативный клиент для REST API
- [curl](https://curl.se/) - консольная утилита для HTTP запросов

### Сервисы для практики

- [reqres.in](https://reqres.in/) - тестовый REST API
- [JSONPlaceholder](https://jsonplaceholder.typicode.com/) - фейковый REST API
- [httpbin.org](https://httpbin.org/) - сервис для тестирования HTTP

### Дополнительное чтение

- [HTTP authentication - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)
- [API testing - Wikipedia](https://en.wikipedia.org/wiki/API_testing)
- [REST API Tutorial](https://restfulapi.net/)
- [HTTP Status Codes](https://httpstatuses.com/)

---

**Ключевые моменты:**
- API - интерфейс для взаимодействия программ
- HTTP - основной протокол для веб-API
- REST - популярный архитектурный стиль
- Тестирование API включает функциональные, производительные и безопасностные аспекты