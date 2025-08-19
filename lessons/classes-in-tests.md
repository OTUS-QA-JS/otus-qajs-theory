# Классы в JavaScript для автоматизированного тестирования

## Содержание
- [Введение в объектно-ориентированный подход](#введение-в-объектно-ориентированный-подход)
- [Паттерн Page Object](#паттерн-page-object)
- [Создание базовых классов страниц](#создание-базовых-классов-страниц)
- [Наследование и иерархия классов](#наследование-и-иерархия-классов)
- [Практические примеры](#практические-примеры)
- [Лучшие практики](#лучшие-практики)
- [Полезные ресурсы](#полезные-ресурсы)

## Введение в объектно-ориентированный подход

**Классы в тестировании** — это способ структурирования и организации кода автотестов с использованием принципов объектно-ориентированного программирования. Они помогают создавать переиспользуемые, поддерживаемые и масштабируемые тестовые решения.

Основные преимущества использования классов в автотестах:
- **Инкапсуляция** — скрытие деталей реализации
- **Повторное использование** — один класс для множества тестов
- **Поддерживаемость** — изменения в одном месте
- **Читаемость** — структурированный и понятный код

## Паттерн Page Object

**Page Object** — это паттерн проектирования, который создает объектную модель для веб-страниц в тестах UI. Каждая страница представлена отдельным классом, который содержит элементы и методы для взаимодействия с ней.

### Основы работы с Page Object

В Playwright объект `Page` представляет вкладку браузера и используется для навигации, кликов, ввода и других действий:

```typescript
import type { Page } from 'playwright-core';

export class LoginPageClass {
  private page: Page;

  constructor(page: Page) {
    this.page = page;
  }
}
```

### Определение элементов страницы

Элементы страницы определяются как приватные свойства класса с использованием локаторов:

```typescript
export class LoginPageClass {
  private page: Page;
  private emailInput: string = 'input-email';
  private passwordInput: string = 'input-password';
  private loginButton: string = 'btn-submit';

  constructor(page: Page) {
    this.page = page;
  }
}
```

### Методы взаимодействия

Каждое действие на странице реализуется как отдельный метод:

```typescript
async navigate(): Promise<void> {
  await this.page.goto('/login');
}

async login(email: string, password: string): Promise<void> {
  await this.page.getByTestId(this.emailInput).fill(email);
  await this.page.getByTestId(this.passwordInput).fill(password);
  await this.page.getByTestId(this.loginButton).click();
}
```

## Создание базовых классов страниц

### Страница авторизации

Полный пример класса для страницы входа в систему:

```typescript
import type { Page } from 'playwright-core';

export class LoginPageClass {
  private page: Page;
  private emailInput: string = 'input-email';
  private passwordInput: string = 'input-password';
  private loginButton: string = 'btn-submit';

  constructor(page: Page) {
    this.page = page;
  }

  async navigate(): Promise<void> {
    await this.page.goto('/login');
  }

  async login(email: string, password: string): Promise<void> {
    await this.page.getByTestId(this.emailInput).fill(email);
    await this.page.getByTestId(this.passwordInput).fill(password);
    await this.page.getByTestId(this.loginButton).click();
  }
}
```

### Страница регистрации

Класс для страницы регистрации нового пользователя:

```typescript
import type { Page } from 'playwright-core';

export class AuthPageClass {
  private page: Page;
  private usernameInput: string = 'input-username';
  private emailInput: string = 'input-email';
  private passwordInput: string = 'input-password';
  private loginButton: string = 'btn-submit';

  constructor(page: Page) {
    this.page = page;
  }

  async navigate(): Promise<void> {
    await this.page.goto('/register');
  }

  async auth(username: string, email: string, password: string): Promise<void> {
    await this.page.getByTestId(this.usernameInput).fill(username);
    await this.page.getByTestId(this.emailInput).fill(email);
    await this.page.getByTestId(this.passwordInput).fill(password);
    await this.page.getByTestId(this.loginButton).click();
  }
}
```

## Наследование и иерархия классов

### Базовый класс

Создание общего родительского класса для всех страниц:

```typescript
import type { Page } from 'playwright-core';

export class BasePageClass {
  protected page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  async navigateTo(url: string): Promise<void> {
    await this.page.goto(url);
  }
}
```

### Наследование от базового класса

Использование наследования для избежания дублирования кода:

```typescript
export class LoginPageClass extends BasePageClass {
  private emailInput: string = 'input-email';
  private passwordInput: string = 'input-password';
  private loginButton: string = 'btn-submit';

  constructor(page: Page) {
    super(page);
  }

  async navigate(): Promise<void> {
    await super.navigateTo('/login');
  }

  async login(email: string, password: string): Promise<void> {
    await this.page.getByTestId(this.emailInput).fill(email);
    await this.page.getByTestId(this.passwordInput).fill(password);
    await this.page.getByTestId(this.loginButton).click();
  }
}
```

### Статические свойства

Использование статических переменных для общих локаторов:

```typescript
export class LoginPageClass extends BasePageClass {
  private static emailInput: string = 'input-email';
  private static passwordInput: string = 'input-password';
  private static loginButton: string = 'btn-submit';

  constructor(page: Page) {
    super(page);
  }

  async login(email: string, password: string): Promise<void> {
    await this.page.getByTestId(LoginPageClass.emailInput).fill(email);
    await this.page.getByTestId(LoginPageClass.passwordInput).fill(password);
    await this.page.getByTestId(LoginPageClass.loginButton).click();
  }
}
```

## Практические примеры

### Класс для редактора статей

Пример более сложного класса с множественными взаимодействиями:

```typescript
export class EditorPageClass extends BasePageClass {
  private titleInput: string = 'Article Title';
  private aboutInput: string = "What's this article about?";
  private contentInput: string = 'Write your article (in)';
  private tagInput: string = 'Enter tags';
  private publishButton = { name: "Publish Article" };

  constructor(page: Page) {
    super(page);
  }

  async visit(slug?: string): Promise<void> {
    await this.page.goto(`/editor/${slug}`);
  }

  async isOpen(slug?: string): Promise<void> {
    await this.page.waitForURL(`/editor/${slug}`);
  }

  async fillAndPublishArticle(title: string, about: string, content: string, tags: string[]): Promise<void> {
    await this.page.getByPlaceholder(this.titleInput).fill(title);
    await this.page.getByPlaceholder(this.aboutInput).fill(about);
    await this.page.getByPlaceholder(this.contentInput).fill(content);
    await this.page.getByPlaceholder(this.tagInput).fill(tags.join(' '));
    await this.page.getByRole("button", this.publishButton).click();
  }
}
```

### Использование в тестах

Примеры тестов с использованием созданных классов:

```typescript
import { test, expect } from '@playwright/test';
import { LoginPageClass, AuthPageClass, EditorPageClass } from '../framework/pages';
import { faker } from '@faker-js/faker';

test('Создание нового пользователя', async ({ page }) => {
  const authPage = new AuthPageClass(page);

  await authPage.navigate();
  await authPage.auth(
    faker.person.fullName(),
    faker.internet.email(),
    'reg1_passw0rd'
  );

  await expect(page).toHaveURL(/\/feed/);
});

test('Успешная авторизация', async ({ page }) => {
  const loginPage = new LoginPageClass(page);

  await loginPage.navigate();
  await loginPage.login(configRWA.email, configRWA.password);

  await expect(page).toHaveURL(/\/feed/);
  await expect(page.getByText('A place to share your')).toBeVisible();
  await expect(page.getByRole('link', { name: 'test test' })).toBeVisible();
});

test('Неуспешная авторизация', async ({ page }) => {
  const loginPage = new LoginPageClass(page);

  await loginPage.navigate();
  await loginPage.login('undefined@mail.ru', 'P@ssW0rd');

  await expect(page).toHaveURL(/\/login\/error/);
  await expect(page.getByText('This page could not be found.')).toBeVisible();
});

test('Создание статьи', async ({ page }) => {
  const editorPage = new EditorPageClass(page);

  await page.getByRole('link', { name: 'New Post' }).click();

  await editorPage.fillAndPublishArticle(
    'article title',
    'about article',
    'article content',
    ['article']
  );

  await expect(page.getByRole('heading')).toContainText('article title');
  await expect(page.getByRole('button', { name: 'Delete Article' })).nth(1).toBeVisible();
});
```

## Лучшие практики

### Структура проекта
Организуйте классы страниц в отдельной директории:
```
framework/
├── pages/
│   ├── BasePageClass.ts
│   ├── LoginPageClass.ts
│   ├── AuthPageClass.ts
│   └── EditorPageClass.ts
└── index.ts
```

### Импорты и экспорты
Используйте barrel files для удобства импорта:

```typescript
// framework/pages/index.ts
export { BasePageClass } from './BasePageClass';
export { LoginPageClass } from './LoginPageClass';
export { AuthPageClass } from './AuthPageClass';
export { EditorPageClass } from './EditorPageClass';

// В тестах
import { LoginPageClass, AuthPageClass } from '../framework/pages';
```

### Принципы разработки
- **Единственная ответственность**: каждый класс отвечает за одну страницу
- **Инкапсуляция**: скрывайте детали реализации локаторов
- **DRY (Don't Repeat Yourself)**: выносите общую функциональность в базовый класс
- **Читаемость**: используйте понятные имена методов и переменных

## Полезные ресурсы
- **Page Object Model**: [Официальная документация Playwright](https://playwright.dev/docs/pom)
- **TypeScript для тестирования**: [Best Practices](https://typescript-lang.org/docs/handbook/testing.html)