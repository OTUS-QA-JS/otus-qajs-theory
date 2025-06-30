# Теория BDD

---
## Что такое BDD

BDD (Behavior-Driven Development) — это методология разработки ПО, основанная на TDD (Test-Driven Development). Её цель — объединить технические и бизнес-интересы, предоставив универсальный язык для общения между разработчиками, тестировщиками и бизнесом.

Ключевые особенности:
*	Используется предметно-ориентированный язык, приближенный к естественному.
*	Поведение системы описывается через сценарии на языке, понятном всем участникам процесса.
*	Сценарии автоматизируются в виде end-to-end тестов.
---
## Сравнение TDD и BDD

Методология TDD

Цель: Обеспечить, чтобы каждая небольшая часть функциональности была покрыта тестами.
Фокус: Уровень кода и технической реализации.
Процесс:
*	Написание юнит-теста, который изначально не проходит.
*	Создание минимального кода, чтобы тест прошёл.
*	Рефакторинг кода с сохранением прохождения тестов.

Методология BDD

Цель: Обеспечить соответствие требованиям бизнеса и фокус на ценность для пользователя.
Фокус: Поведение системы с точки зрения пользователя.
Процесс:
*	Определение сценариев на понятном языке (Given-When-Then).
*	Автоматизация сценариев как интеграционных тестов.
*	Реализация функционала под успешное выполнение сценариев.

Отличия
*	TDD работает на уровне компонентов и методов, ориентирован на разработчиков.
*	BDD — это коммуникация между всеми участниками процесса.
*	И там, и там тесты пишутся до кода, но BDD — это поведение, TDD — реализация.
---
## Основные элементы BDD

План теста
1.	Заголовок (Title) — формулируется как цель бизнес-истории.
2.	Описание (Narrative) — отвечает на вопросы:
3.	Кто заинтересован?
4.	Что входит в историю?
5.	Какую бизнес-ценность это несёт?
6.  Сценарии (Scenarios) — описывают конкретные случаи поведения.

Пример сравнения плана тестов BDD и TDD

Заголовок:

* TDD — Проверка валидации входа на уровне бэкенда
* BDD — Функциональность входа в систему

Описание:

* TDD — Как разработчик, мне нужно убедиться, что бэкенд корректно валидирует логин
* BDD — Как пользователь, я хочу войти в систему, чтобы получить доступ к панели

Сценарии:

* TDD — Тест: Корректные данные возвращают статус 200 и токен
* BDD — Тест: Пользователь вводит корректные данные, происходит редирект

---

## Язык Gherkin

Gherkin — язык описания поведения в BDD. Читаемый, структурированный, с ключевыми словами.

Пример:
``` 
Feature: Login functionality
  Scenario: Successful login with valid credentials
    Given the user is on the login page
    When they enter a valid username and password
    Then they are redirected to their dashboard

Scenario: Login attempt with invalid credentials
    Given the user is on the login page
    When they enter an invalid username or password
    Then an error message “Invalid username or password” is displayed
```

Ключевые слова Gherkin
*	Feature / История — начало спецификации
*	Scenario / Сценарий — новый сценарий
*	Given / Дано — начальные условия
*	When / Когда — действие
*	Then / Тогда — результат
*	And / И — добавление условий
*	But / Но — исключение

---

## Инструменты

Cucumber
*	Использует .feature файлы
*	Поддерживает Gherkin
*	Автоматизирует описание поведения

CodeceptJS - фреймворк для end-to-end тестов, работающий с:
*	Playwright
*	WebDriver
*	Puppeteer
*	TestCafe

Поддерживает BDD через Gherkin и Cucumber.
```
Scenario Outline

Scenario Outline: Cash withdrawal
  When user selects  option
  Then ATM should issue  banknotes

Examples:
  | exchange | quantity |
  | large    | 1        |
  | small    | 5        |
```
---

## Практика: CodeceptJS

Команды

* npx create-codeceptjs .
* codeceptjs run –steps
* codecept-ui –app

Пример теста
```
Feature(‘Login’);
Scenario(‘test something’, ({ I }) => {
  I.amOnPage(’/login’);
  I.fillField(’[testid=“login”]’, ‘username’);
  I.click(’[data-testid=“btn-submit”]’);
});
```
Page Object пример
```
export = {
  visit() {
    I.amOnPage(’/Login’);
  },
  fillEmail(email) {
    I.fillField(’[data-testid=“input-email”]’, email);
  },
  fillPassword(password) {
    I.fillField(’[data-testid=“input-password”]’, password);
  },
  submitForm() {
    I.click(’[data-testid=“btn-submit”]’);
  },
  login({ email, password }) {
    this.visit();
    this.fillEmail(email);
    this.fillPassword(password);
    this.submitForm();
  }
}
```
Конфигурация
```
module.exports = {
  credentials: {
    user: {
      email: ‘root@mail.net’,
      password: ‘E5dPKcF7bPTnfn6q’
    }
  },
  baseUrl: ‘https://rwa-194.87.102.103.sslip.io’
};
```
---
## Сравнение Playwright и CodeceptJS
| Критерий            | Playwright                                  | CodeceptJS                                                  |
|---------------------|---------------------------------------------|--------------------------------------------------------------|
| Уровень контроля    | Низкоуровневое управление                   | Высокоуровневый интерфейс, удобно для BDD                    |
| Простота написания  | Требует больше кода и ручной настройки      | Лаконичный код, много автоматизации                          |
| Скорость разработки | Может быть медленным для новичков           | Ускоряет работу, особенно в крупных проектах                |
| BDD                 | Отсутствует встроенная поддержка            | Поддержка Gherkin и Cucumber                                 |
---
## Настройка проекта

Установка через терминал:
* npx codeceptjs init
* yes (TypeScript)
* tests/*test.ts
* Playwright
* reports
* English (вводим в терминале)
* chromium
* https://rwa-194.87.102.103.sslip.io
* yes
* login
* login_test.ts
---

## Структура проекта
*	Необходимо просмотреть папку tests
*	Проверить файл codecept.conf.ts


Работа с login_test.ts
Можно сравнить:
*	Feature — это describe в Jest
*	Scenario — это it

Внутри login_test.ts можно писать I, и через точку вызывать методы, например:
```
I.amOnPage(’/login’);
I.fillField(‘input[name=“username”]’, ‘user’);
I.click(‘Login’);
```
---

Варианты клика по кнопке

Вариант 1 — просто CSS-селектор (клик по первому найденному элементу button)
```
I.click(“button”)
```

Вариант 2 — уточнение по тексту (клик по кнопке с текстом “Login”)
```
I.click(‘Login’, ‘button’)
```

Вариант 3 — через data-testid
```
I.click(’[data-testid=“btn-submit”]’)
```
---

## Расширение I (актора)

I — это обёртка над Codecept, его можно расширить.
В файле steps.file.ts можно определить пользовательские шаги, например, login.


Добавление PageObject, команда:
```
npx codeceptjs gpo
Name: LoginPage
Расположение: ./pages/LoginPage.ts
Object type: module
```

Проверка конфигурации

Перейти в codecept.conf.ts, проверить, что:
*	подключён путь к PageObject
*	в include указано: "loginPage": "./pages/LoginPage.ts"

Также проверить файл steps.d.ts, и если страница не появилась — выполнить:
```
npx codeceptjs def
```
---

## Структура LoginPage.ts

Добавляем методы:
```
visit()
fillEmail(email)
fillPassword(password)
submitForm()
```

Также можно добавить метод login с использованием конфига.

---

## Конфигурация проекта

Добавляем файл config.ts в корне:

config.ts:
```
module.exports = {
  credentials: {
    user: {
      email: ‘root@mail.net’,
      password: ‘E5dPKcF7bPTnfn6q’
    }
  },
  baseUrl: ‘https://rwa-194.87.102.103.sslip.io’
};
```
Далее импортируем конфиг и используем креды в loginPage.

---
Дальнейшие шаги
1.	В LoginPage реализуем login(email, password)
2.	В login_test.ts обращаемся к loginPage.login()
3.	Можно вызывать методы PageObject внутри теста

--

Несколько дополнительных тестов

* Пишутся в login_test.ts
* Каждый тест — новый Scenario
Например:
```
Scenario(‘empty fields shows validation’, async ({ I }) => {
  I.amOnPage(’/login’);
  I.click(‘Submit’);
  I.see(‘Please enter email’);
});
```
---

## Интеграция Gherkin

Инициализация:
```
npx codeceptjs gherkin:init
```
Появляются пути в codecept.conf.ts:
*	где искать .feature файлы
*	где искать step definitions

---

Пример feature-файла

Путь: features/basic.feature
```
Feature: Login
  Background:
    Given I am on the login page

Scenario: Successful login
    When I enter correct credentials
    Then I should be redirected to dashboard
```
---

Использование Scenario Outline

Для параметризованных тестов:
```
Scenario Outline: Login combinations
  When I try to login with  and 
  Then I see

Examples:
  | email         | password   | result              |
  | test@test.com | wrongpass | error message shown |
  | admin@test.com| correct123| dashboard shown     |
```
---

## Step definitions

Реализуются в step_definitions/steps.ts
Каждый шаг из feature-файла должен быть описан функцией.

Пример:
```
Given(‘I am on the login page’, async ({ I }) => {
  I.amOnPage(’/login’);
});
```
---

## Запуск тестов

Можно запустить одним из вариантов
* npx codeceptjs run –features
* npm run codeceptjs:ui

---

Сравнение: обычные тесты vs Gherkin

tests/login_test.ts:

* Пишутся на чистом CodeceptJS API.
* Подход: больше контроля, быстрее писать, гибче логика.
* Не требуется feature-файлов.

step_definitions/steps.ts:

* Используется с .feature файлами.

Преимущества:
* 	Читаемо всем (включая бизнес)
* 	Повторно используемые шаги
* 	Унификация подхода

---

## Когда использовать что?

Используй Gherkin, если:
* 	Нужна читаемость для бизнеса
* 	Требуется согласование сценариев

Используй обычные тесты, если:
* 	Работаешь только с QA и разработчиками
* 	Нужна гибкость

---

## Итог

Оба подхода можно совмещать.
* 	Gherkin хорош для документации и согласования
* 	Чистый CodeceptJS — для технических, быстрых и сложных тестов