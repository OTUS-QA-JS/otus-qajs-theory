# Валидация ответов API

## Содержание
- [Документация API](#документация-api)
- [Валидация с помощью Jest Matchers](#валидация-с-помощью-jest-matchers)
- [Валидация на основе схемы](#валидация-на-основе-схемы)
- [Полезные ресурсы](#полезные-ресурсы)

## Документация API

Четкая документация является основой для тестирования API. Она описывает эндпоинты, методы, структуры запросов и ответов.

### Спецификация OpenAPI (Swagger)
**OpenAPI** (ранее известная как Swagger) — это стандарт де-факто для описания и документирования RESTful API. Спецификация позволяет точно определить все ресурсы API и их параметры, что делает ее основой для генерации клиентского кода, документации и автоматизированных тестов.

### Альтернативные подходы
- **TypeSpec**: Современная альтернатива от Microsoft для описания API.
- **RAML** и **API Blueprint**: Исторические форматы, которые могут встречаться в legacy-проектах, но в настоящее время не развиваются активно.
- **Внутренняя документация**: Описание API в Confluence, Wiki или других системах.

## Валидация с помощью Jest Matchers

Jest предоставляет встроенные "матчеры" (matchers), которые позволяют проводить проверки ответов API.

### Базовые проверки
Для простых проверок используются матчеры `.toBe()`, `.toEqual()`, `.toBeDefined()` и другие.

```javascript
it('Успешная авторизация', async () => {
  const response = await AuthService.generateToken(...);

  expect(response.status).toBe(200);
  expect(response.data.result).toBe('User authorized successfully.');
  expect(response.data.token).toBeDefined();
});
```

### `.toHaveProperty(keyPath, [value])`
Этот матчер проверяет наличие свойства в объекте. Он особенно удобен для проверки вложенных свойств без создания длинных цепочек.

```javascript
it('Успешная авторизация', async () => {
  const response = await AuthService.generateToken(...);

  expect(response).toHaveProperty('status', 200);
  expect(response).toHaveProperty('data.result', 'User authorized successfully.');
  expect(response).toHaveProperty('data.token'); // Проверяем только наличие ключа
});
```

### `.toMatchObject(object)`
Позволяет проверить, что объект содержит определенный набор свойств (частичная проверка). Это полезно, когда в ответе есть динамически изменяющиеся данные (например, даты, ID, токены), которые мы не хотим проверять на точное соответствие.

Для динамических значений можно использовать асимметричные матчеры, такие как `expect.any(String)` или `expect.anything()`.

```javascript
it('Успешная авторизация', async () => {
  const response = await AuthService.generateToken(...);

  expect(response.status).toBe(200);
  expect(response.data).toMatchObject({
    result: 'User authorized successfully.',
    // Проверяем только тип, но не конкретное значение
    token: expect.any(String),
    expires: expect.any(String),
  });
});
```

## Валидация на основе схемы

Когда ответы API имеют сложную структуру, ручная проверка каждого поля становится неэффективной. В таких случаях используется валидация по схеме, которая описывает ожидаемую структуру, типы данных и форматы.

### JSON Schema
**JSON Schema** — это стандарт для описания структуры JSON-документов. С его помощью можно определить:
- Типы данных для каждого поля (`string`, `number`, `object`, `array`, `boolean`, `null`).
- Обязательные и необязательные поля.
- Структуру вложенных объектов и массивов.
- Форматы данных (например, `date-time`, `email`, `uri`).

**Пример JSON Schema:**
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "id": { "type": "number" },
    "quote": { "type": "string" },
    "author": { "type": "string" }
  },
  "required": ["id", "quote", "author"],
  "additionalProperties": false
}
```

### JSON Type Definition (JTD)
**JTD** — это более новая и простая альтернатива JSON Schema, также предназначенная для определения формы JSON-данных.

### Библиотека AJV
**AJV (Another JSON Schema Validator)** — это быстрая и популярная библиотека для валидации данных по спецификациям JSON Schema и JTD.

**Пример использования AJV в тестах:**

1.  **Установка**:
    ```bash
    npm install ajv
    ```

2.  **Написание теста**:
    ```javascript
    import Ajv from 'ajv';
    const ajv = new Ajv();

    // Схема, описывающая ожидаемый ответ
    const quoteSchema = {
      type: 'object',
      properties: {
        id: { type: 'number' },
        quote: { type: 'string' },
        author: { type: 'string' },
      },
      required: ['id', 'quote', 'author'],
      additionalProperties: false, // Запрещаем лишние поля в ответе
    };

    it('должен возвращать цитату в правильном формате', async () => {
      const response = await QuoteService.getRandomQuote();
      const validate = ajv.compile(quoteSchema);
      const isValid = validate(response.data);

      expect(isValid).toBe(true);
      // Для более детального отчета об ошибках
      // if (!isValid) console.log(validate.errors);
    });
    ```

### Альтернативы AJV
- **Joi**
- **Yup**

## Полезные ресурсы
- **Swagger**: [Что это такое и как использовать](https://yandex.ru/q/article/swagger_chto_eto_takoe_ispolzovanie_dlia_e43c3969/)
- **OpenAPI и тестирование**: [Статья на testengineer.ru](https://testengineer.ru/open-api/)
- **Learn JSON Typedef in 5 Minutes**: [Быстрое введение в JTD](https://jsontypedef.com/docs/learn-in-5-minutes/)
- **Postman и JSON Schema**: [Примеры валидации в Postman](https://learning.postman.com/docs/writing-scripts/post-response-scripts/)