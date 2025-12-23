# Основы JavaScript

## Цель

Практика работы с JavaScript, написание и использование функций, работа со случайными значениями.

---

## Задание

Необходимо реализовать функцию генерации email.
Можно выбрать **один из двух вариантов** выполнения задания.

* **Вариант 1** — базовый
* **Вариант 2** — повышенной сложности (больше практики с JS / Node.js)

---

## Вариант 1 (базовый)

### Условие

Создайте файл:

```
framework/fixtures/User.js
```

В файле `User.js` реализуйте функцию `generateEmail`, которая генерирует случайный email.

### Формат email

Все email должны иметь вид:

```
user-<5 случайных цифр>@domain.localhost
```

Пример:

```
user-12345@domain.localhost
```

### Подсказка

Для генерации случайного числа можно использовать модуль `node:crypto`:

```js
import { randomInt } from 'node:crypto';

const n = randomInt(10_000, 100_000); // число из 5 цифр
```

### Пример использования

```js
const email = generateEmail();
console.log(email);
// user-12345@domain.localhost
```

### Полезные ссылки

* [Функции в JavaScript](https://learn.javascript.ru/function-basics)

---

## Вариант 2 (расширенный)

### Условие

Создайте файл:

```
framework/fixtures/User.js
```

В файле `User.js` реализуйте функцию `generateEmail`, которая генерирует случайный email с учётом переданных параметров.

### Формат email

```
username@domain.localhost
```

`username` формируется на основе входных параметров и случайного числа.

---

### Сигнатура функции

```ts
function generateEmail(
  options?: {
    firstName?: string;
    lastName?: string;
    provider?: string;
  }
): string;
```

* `firstName` — имя пользователя
* `lastName` — фамилия пользователя
* `provider` — домен email (по умолчанию `domain.localhost`)

---

### Примеры ожидаемого поведения

```ts
generateEmail();
// 'User42@domain.localhost'

generateEmail({ firstName: 'Jeanne' });
// 'Jeanne63@domain.localhost'

generateEmail({ firstName: 'Jeanne', lastName: 'Doe' });
// 'Jeanne.Doe88@domain.localhost'

generateEmail({
  firstName: 'Jeanne',
  lastName: 'Doe',
  provider: 'example.otus.dev',
});
// 'Jeanne.Doe91@example.otus.dev'

generateEmail({
  firstName: 'Jeanne',
  lastName: 'Doe',
  provider: 'example.otus.dev',
  allowSpecialCharacters: false,
});
// 'JeanneDoe77@example.otus.dev'
```

> Конкретный формат `username` (точка, подчёркивание, их отсутствие) остаётся на ваше усмотрение, если не противоречит примерам.

---

## Критерии оценивания

* Функция реализована корректно
* Возвращаемый email соответствует описанному формату
* Обрабатываются необязательные параметры
* Код читаемый и аккуратный
