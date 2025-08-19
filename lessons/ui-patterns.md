# Паттерны проектирования в UI-тестировании

## Содержание
- [Page Object Model (POM)](#page-object-model-pom)
- [Альтернатива: Application Actions](#альтернатива-application-actions)
- [Паттерн Screenplay](#паттерн-screenplay)
- [Полезные ресурсы](#полезные-ресурсы)

## Page Object Model (POM)

**Page Object Model (POM)** — это широко распространенный паттерн проектирования в автоматизации тестирования UI, который предназначен для улучшения читаемости, поддерживаемости и переиспользования тестового кода.

Основная идея паттерна — создать набор функций для каждой страницы (или значимого компонента) веб-приложения. Эти функции инкапсулируют все детали взаимодействия с UI этой страницы.

### Основные принципы

1. **Абстракция страницы**: Каждая страница или переиспользуемый компонент интерфейса (например, хедер, форма логина) представляется в виде набора функций. Эти функции называются **Page Object**.

2. **Инкапсуляция локаторов и взаимодействий**: Page Object хранит локаторы всех элементов на странице и предоставляет высокоуровневые функции для взаимодействия с ними. Тесты не должны работать с локаторами напрямую.

   - **Плохо (в тесте)**: `page.locator('#user-name').fill('user');`
   - **Хорошо (в тесте)**: `await fillUsername(page, 'user');`

3. **Разделение ответственности**: Паттерн помогает отделить логику теста (ЧТО нужно проверить) от логики взаимодействия с интерфейсом (КАК это сделать). Тесты становятся декларативными — они описывают шаги пользователя, а не детали реализации.

### Реализация с помощью функций-фабрик

```javascript
// tests/pages/loginPage.js

/**
 * Фабрика для создания объекта страницы логина
 * @param {Object} params - Параметры
 * @param {import('@playwright/test').Page} params.page - Объект страницы Playwright
 * @returns {Object} Объект с методами для работы со страницей логина
 */
export function LoginPage({ page }) {
  // Локаторы элементов страницы
  const selectors = {
    emailInput: '#email',
    passwordInput: '#password',
    loginButton: '#login-button',
    errorMessage: '.error-message'
  };

  // Методы для взаимодействия со страницей
  const visit = async () => {
    await page.goto('/login');
  };

  const fillEmail = async (email) => {
    await page.locator(selectors.emailInput).fill(email);
  };

  const fillPassword = async (password) => {
    await page.locator(selectors.passwordInput).fill(password);
  };

  const submit = async () => {
    await page.locator(selectors.loginButton).click();
  };

  const getErrorMessage = async () => {
    return await page.locator(selectors.errorMessage).textContent();
  };

  // Композитный метод для полной авторизации
  const login = async (email, password) => {
    await fillEmail(email);
    await fillPassword(password);
    await submit();
  };

  // Возвращаем объект с публичными методами
  return {
    visit,
    fillEmail,
    fillPassword,
    submit,
    login,
    getErrorMessage
  };
}

### Использование в тестах

```javascript
// tests/specs/login.spec.js
import { test, expect } from '@playwright/test';
import { LoginPage } from '../pages/loginPage.js';

test('успешная авторизация', async ({ page }) => {
  const loginPage = LoginPage({ page });
  
  await loginPage.visit();
  await loginPage.login('user@example.com', 'password123');
  
  await expect(page).toHaveURL('/dashboard');
});

test('неуспешная авторизация с неверным паролем', async ({ page }) => {
  const loginPage = LoginPage({ page });
  
  await loginPage.visit();
  await loginPage.login('user@example.com', 'wrongpassword');
  
  const errorText = await loginPage.getErrorMessage();
  expect(errorText).toContain('Неверные учетные данные');
});

test('пошаговое заполнение формы', async ({ page }) => {
  const loginPage = LoginPage({ page });
  
  await loginPage.visit();
  
  await loginPage.fillEmail('user@example.com');
  await expect(page.locator('#email')).toHaveValue('user@example.com');
  
  await loginPage.fillPassword('password123');
  await expect(page.locator('#password')).toHaveValue('password123');
  
  await loginPage.submit();
  await expect(page).toHaveURL('/dashboard');
});
```

### Организация кода

Для удобства импорта можно создать индексный файл:

```javascript
// tests/pages/index.js
export * from './loginPage.js';
export * from './dashboardPage.js';
export * from './profilePage.js';
```

```javascript
// tests/specs/login.spec.js
import { test, expect } from '@playwright/test';
import { LoginPage } from '../pages/index.js';

test('успешная авторизация', async ({ page }) => {
  const loginPage = LoginPage({ page });
  
  await loginPage.visit();
  await loginPage.login('user@example.com', 'password123');
  
  await expect(page).toHaveURL('/dashboard');
});
```

### Преимущества функционального подхода

- **Простота понимания**: Функции легче понять студентам, которые еще не изучали классы
- **Гибкость**: Можно импортировать только необходимые функции
- **Композиция**: Легко комбинировать мелкие функции в более сложные
- **Поддерживаемость**: Если локатор или элемент UI изменяется, правки нужно внести только в одном месте
- **Читаемость**: Тесты становятся более понятными, так как они оперируют бизнес-терминами
- **Переиспользование кода**: Функции можно многократно использовать в разных тестах

### Продвинутые возможности

Можно создавать более сложные функции, которые возвращают объекты с несколькими свойствами:

```javascript
// tests/pages/productPage.js

/**
 * Получает информацию о продукте
 * @param {import('@playwright/test').Page} page 
 * @returns {Promise<{name: string, price: string, description: string}>}
 */
export async function getProductInfo(page) {
  const name = await page.locator('.product-name').textContent();
  const price = await page.locator('.product-price').textContent();
  const description = await page.locator('.product-description').textContent();
  
  return { name, price, description };
}

/**
 * Добавляет товар в корзину с указанным количеством
 * @param {import('@playwright/test').Page} page 
 * @param {number} quantity 
 */
export async function addToCart(page, quantity = 1) {
  if (quantity > 1) {
    await page.locator('.quantity-input').fill(quantity.toString());
  }
  await page.locator('.add-to-cart-button').click();
}
```

## Альтернатива: Application Actions

**Application Actions** (или App Actions) — это подход, который является альтернативой или дополнением к POM. Вместо того чтобы моделировать страницы, этот паттерн фокусируется на моделировании действий или бизнес-процессов, которые пользователь может выполнить в приложении.

### Идея подхода

Создаются функции, которые инкапсулируют целые пользовательские сценарии, часто состоящие из нескольких шагов и охватывающие несколько страниц.

### Пример реализации

```javascript
// tests/actions/authActions.js

/**
 * Выполняет полную авторизацию пользователя через UI
 * @param {import('@playwright/test').Page} page 
 * @param {string} username 
 * @param {string} password 
 */
export async function loginViaUI(page, username, password) {
  await page.goto('/login');
  await page.locator('#username').fill(username);
  await page.locator('#password').fill(password);
  await page.locator('#login-button').click();
  
  // Ждем перехода на главную страницу
  await page.waitForURL('/dashboard');
}

/**
 * Быстрая авторизация через API (для ускорения тестов)
 * @param {import('@playwright/test').Page} page 
 * @param {string} username 
 * @param {string} password 
 */
export async function loginViaAPI(page, username, password) {
  // Получаем токен через API
  const response = await page.request.post('/api/auth/login', {
    data: { username, password }
  });
  
  const { token } = await response.json();
  
  // Устанавливаем токен в localStorage
  await page.addInitScript((token) => {
    localStorage.setItem('authToken', token);
  }, token);
  
  // Переходим на главную страницу
  await page.goto('/dashboard');
}

/**
 * Регистрация нового пользователя
 * @param {import('@playwright/test').Page} page 
 * @param {Object} userData 
 */
export async function registerUser(page, userData) {
  await page.goto('/register');
  await page.locator('#email').fill(userData.email);
  await page.locator('#username').fill(userData.username);
  await page.locator('#password').fill(userData.password);
  await page.locator('#confirm-password').fill(userData.password);
  await page.locator('#register-button').click();
  
  // Ждем подтверждения регистрации
  await expect(page.locator('.success-message')).toBeVisible();
}
```

### Использование в тестах

```javascript
// tests/specs/userFlow.spec.js
import { test, expect } from '@playwright/test';
import { loginViaUI, loginViaAPI, registerUser } from '../actions/authActions.js';

test('пользователь может зарегистрироваться и войти', async ({ page }) => {
  // Используем action для регистрации
  await registerUser(page, {
    email: 'test@example.com',
    username: 'testuser',
    password: 'password123'
  });
  
  // Используем action для входа
  await loginViaUI(page, 'testuser', 'password123');
  
  await expect(page.locator('.welcome-message')).toBeVisible();
});

test('быстрый вход через API', async ({ page }) => {
  // Используем быстрый вход для настройки состояния
  await loginViaAPI(page, 'testuser', 'password123');
  
  // Проверяем, что пользователь авторизован
  await expect(page.locator('.user-menu')).toBeVisible();
});
```

Этот подход делает тесты еще более высокоуровневыми и сфокусированными на поведении пользователя, а не на структуре страниц.

## Паттерн Screenplay

**Паттерн Screenplay** — это дальнейшее развитие идей POM и Application Actions, которое ставит во главу угла пользователя (актора) и его цели. Он предлагает более строгую и масштабируемую структуру для описания тестов, основанную на принципах SOLID.

### Идея паттерна

Тесты пишутся с точки зрения пользователя (Актора), который выполняет Задачи для достижения своих Целей. Каждая Задача состоит из более мелких Взаимодействий с системой. Акторы также могут задавать Вопросы о состоянии системы.

### Основные компоненты

- **Actor (Актор)**: Пользователь, который взаимодействует с системой. У каждого актора есть свои "способности".
- **Abilities (Способности)**: Инструменты, которые позволяют актору взаимодействовать с системой (например, `BrowseTheWeb` с помощью Playwright или `CallAnApi` с помощью Axios).
- **Tasks (Задачи)**: Высокоуровневые бизнес-задачи, состоящие из нескольких шагов (например, `PurchaseItem` или `RegisterNewUser`).
- **Interactions (Взаимодействия)**: Низкоуровневые действия, которые изменяют состояние системы (например, `Click`, `EnterText`).
- **Questions (Вопросы)**: Запросы на получение информации о состоянии системы (например, `Text.of(element)`, `Value.of(input)`). Ответы на вопросы проверяются в ассертах.

### Упрощенная реализация на функциях

```javascript
// tests/screenplay/interactions.js

/**
 * Взаимодействие: клик по элементу
 * @param {string} locator 
 * @returns {Function}
 */
export function click(locator) {
  return async (page) => {
    await page.locator(locator).click();
  };
}

/**
 * Взаимодействие: ввод текста
 * @param {string} locator 
 * @param {string} text 
 * @returns {Function}
 */
export function enterText(locator, text) {
  return async (page) => {
    await page.locator(locator).fill(text);
  };
}

/**
 * Взаимодействие: переход на страницу
 * @param {string} url 
 * @returns {Function}
 */
export function navigate(url) {
  return async (page) => {
    await page.goto(url);
  };
}
```

```javascript
// tests/screenplay/tasks.js
import { navigate, enterText, click } from './interactions.js';

/**
 * Задача: авторизация
 * @param {string} username 
 * @param {string} password 
 * @returns {Function}
 */
export function login(username, password) {
  return async (page) => {
    await navigate('/login')(page);
    await enterText('#username', username)(page);
    await enterText('#password', password)(page);
    await click('#login-button')(page);
  };
}

/**
 * Задача: поиск товара
 * @param {string} searchTerm 
 * @returns {Function}
 */
export function searchForProduct(searchTerm) {
  return async (page) => {
    await enterText('.search-input', searchTerm)(page);
    await click('.search-button')(page);
  };
}
```

```javascript
// tests/screenplay/questions.js

/**
 * Вопрос: получить текст элемента
 * @param {string} locator 
 * @returns {Function}
 */
export function textOf(locator) {
  return async (page) => {
    return await page.locator(locator).textContent();
  };
}

/**
 * Вопрос: проверить видимость элемента
 * @param {string} locator 
 * @returns {Function}
 */
export function isVisible(locator) {
  return async (page) => {
    return await page.locator(locator).isVisible();
  };
}
```

### Использование в тестах

```javascript
// tests/specs/screenplay.spec.js
import { test, expect } from '@playwright/test';
import { login, searchForProduct } from '../screenplay/tasks.js';
import { textOf, isVisible } from '../screenplay/questions.js';

test('успешный поиск товара после авторизации', async ({ page }) => {
  // Актор выполняет задачи
  await login('testuser', 'password123')(page);
  await searchForProduct('laptop')(page);
  
  // Актор задает вопросы системе
  const searchResults = await textOf('.search-results-count')(page);
  const isResultsVisible = await isVisible('.product-list')(page);
  
  // Проверяем ответы
  expect(searchResults).toContain('найдено');
  expect(isResultsVisible).toBe(true);
});
```

### Преимущества

- **Высокая читаемость**: Тесты читаются как проза, описывающая действия пользователя
- **Масштабируемость**: Четкое разделение на мелкие, переиспользуемые компоненты (задачи, взаимодействия) позволяет легко строить сложные сценарии
- **Принцип единой ответственности (SRP)**: Каждая функция имеет одну, четко определенную цель
- **Композиция**: Легко комбинировать простые функции в сложные задачи

## Полезные ресурсы

- **Playwright Docs**: [Page object models](https://playwright.dev/docs/pom)
- **Cypress Blog**: [Stop using Page Objects and start using App Actions](https://www.cypress.io/blog/2019/01/03/stop-using-page-objects-and-start-using-app-actions/)
- **Serenity/JS**: [Официальный сайт фреймворка, реализующего Screenplay Pattern](https://serenity-js.org/handbook/design/screenplay-pattern.html)
- **JavaScript Functions**: [MDN Web Docs о функциях](https://developer.mozilla.org/ru/docs/Web/JavaScript/Guide/Functions)