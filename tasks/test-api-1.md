# АПИ тесты

## Цель
Цель: отработка на практике АПИ-тестирование.

## Задание
В папке `specs` создайте файл  `api.spec.js`

В этом задании есть два варианта, выберете, тот, что вам больше подходит:

**Вариант 1:**

Напишите 5 апи-тестов на сервис `bookstore`
https://bookstore.demoqa.com/swagger/

Напишите АПИ-тесты:
1. [Создание пользователя](https://bookstore.demoqa.com/swagger/#/Account/AccountV1UserPost) c ошибкой, логин уже используется
2. [Создание пользователя](https://bookstore.demoqa.com/swagger/#/Account/AccountV1UserPost) c ошибкой, пароль не подходит
3. [Создание пользователя](https://bookstore.demoqa.com/swagger/#/Account/AccountV1UserPost) успешно
4. [Генерация токена](https://bookstore.demoqa.com/swagger/#/Account/AccountV1GenerateTokenPost) c ошибкой
5. [Генерация токена](https://bookstore.demoqa.com/swagger/#/Account/AccountV1GenerateTokenPost) успешно

**Вариант 2:**
Напишите 5 апи-тестов на любой сервис имеющий публичное АПИ, можно использовать АПИ рабочего проекта.


Для проверки (ревью) домашнего задания присылайте в чат ссылку на `pull request`

Полезные ссылки:
1. [Тестирование асинхронного кода в jest с использованием оператора async / await](https://jestjs.io/docs/asynchronous#asyncawait)
2. [Axios библиотека для отправки запросов (при выполнении ДЗ вы можете использовать другое решение)](https://axios-http.com/docs/intro)
3. [Использование ECMAScript-модулей в Node.js](https://habr.com/ru/company/ruvds/blog/556744/)

---
## Критерии оценивания
1. Тесты работают.
2. Есть проверка всех апи-ручек из ДЗ.