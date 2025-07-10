# Основы JS. Классы в типовых сценариях автотестов


### Перенос теста в классы
Начальный тест: /e2e/auth.spec.ts

Перенос теста в классы
* Создаём LoginPageClass в директории pages
* Импортируем Page и создаём класс
```
import { Page } from “playwright-core”;

export class LoginPageClass {
  private page: Page;

constructor(page: Page) {
   this.page = page
  }
 }
```
---
### Что такое Page
* Это объект Playwright, представляющий вкладку браузера.
* Используется для навигации, кликов, ввода и т.д.
* Импортируется как тип:
```
import type { Page } from 'playwright-core';
```
---

### Страница авторизации
```
export class AuthPageClass {
  private page: Page;

constructor(page: Page) {
   this.page = page;
  }
 }
```
---

### Свойства и методы

Нам нужны:
* email
* password
* кнопка логина

Добавим их в LoginPageClass:
```
private emailInput: string = ‘input-email’;
private passwordInput: string = ‘input-password’;
private loginButton: string = ‘btn-submit’;
```
---
### Метод перехода на страницу
```
async navigate(): Promise {
  await this.page.goto(’/login’);
 }
```
---
### Метод логина
```
async login(email: string, password: string): Promise {
  await this.page.getByTestId(this.emailInput).fill(email)
  await this.page.getByTestId(this.passwordInput).fill(password)
  await this.page.getByTestId(this.loginButton).click()
 }
```
---

### Перенос AuthPage

Создаём AuthPageClass.ts, оставляем только импорт и класс:
```
import type { Page } from ‘playwright-core’;

export class AuthPageClass {
 }
```
Добавляем переменные и конструктор:
```
private usernameInput: string = ‘input-username’;
 private emailInput: string = ‘input-email’;
 private passwordInput: string = ‘input-password’;
 private loginButton: string = ‘btn-submit’;

constructor(page: Page) {
  this.page = page;
 }
```
---

### Методы для регистрации

Метод navigate:
```
async navigate(): Promise {
  await this.page.goto(’/register’);
 }
```
Метод регистрации:
```
async auth(username: string, email: string, password: string): Promise {
  await this.page.getByTestId(this.usernameInput).fill(username)
  await this.page.getByTestId(this.emailInput).fill(email)
  await this.page.getByTestId(this.passwordInput).fill(password)
  await this.page.getByTestId(this.loginButton).click()
 }
```
---

### Создание authClass.spec.ts

Копируем auth.spec.ts → authClass.spec.ts.

Подключаем классы:
```
import { LoginPage, AuthPage } from ‘../framework’
import LoginPageClass from ‘../framework/pages/LoginPageClass’
import AuthPageClass from ‘../framework/pages/AuthPageClass’
```
Создание экземпляра:
```
const authPage = new AuthPageClass(page);
```
---

### Первый тест
```
test(‘Создание нового юзера на классах’, async ({ page }) => {
  const authPage = new AuthPageClass(page)

await authPage.navigate()
  await authPage.auth(faker.person.fullName(), faker.internet.email(), ‘reg1_passw0rd’)

await expect(page).toHaveURL(//feed-feed/)
 })
```
---

### Второй тест
```
test(‘Успешная авторизация’, async ({ page }) => {
  const LoginPage = new LoginPageClass(page)

await LoginPage.navigate()
  await LoginPage.login(configRWA.email, configRWA.password)

await expect(page).toHaveURL(//feed-feed/)
  await expect(page.getByText(‘A place to share your’)).toBeVisible()
  await expect(page.getByRole(‘link’, { name: ‘test test’ })).toBeVisible()
 })
```
---
### Третий тест
```
test(‘Несуществующий пользователь, не может зайти в систему’, async ({ page }) => {
  const loginPage = new LoginPageClass(page)

await loginPage.navigate()
  await loginPage.login(‘undefined@mail.ru’, ‘P@ssW0rd’)

await expect(page).toHaveURL(//login/error/)
  await expect(page.getByText(‘This page could not be found.’)).toBeVisible()
 })
```
---

### Создаём базовый класс

Файл: BasePageClass.ts
```
export class BasePageClass {
  protected page: Page;

constructor(page: Page) {
   this.page = page;
  }

async navigateTo(url: string): Promise {
   await this.page.goto(url);
  }
 }
```
---

### Наследование

В дочернем классе:
```
export class LoginPageClass extends BasePageClass {
  …

constructor(page: Page) {
   super(page)
  }

async navigate(): Promise {
   await super.navigateTo(’/login’);
  }
 }
```
---

### static переменные
```
private static emailInput: string = ‘input-email’;
```
Использование:
```
await this.page.getByTestId(LoginPageClass.emailInput).fill(email)
```
---

### EditorPageClass

Полный класс:
```
export class EditorPageClass extends BasePageClass {
  private titleInput: string = ‘Article Title’
  private aboutInput: string = “What’s this article about?”
  private contentInput: string = ‘Write your article (in)’
  private tagInput: string = ‘Enter tags’
  private publishButton = { name: “Publish Article” }

constructor(page: Page) {
   super(page)
  }

async visit(slug?: string): Promise {
   await this.page.goto(/editor/${slug});
  }

async isOpen(slug?: string): Promise {
   await this.page.waitForURL(/editor/${slug});
  }

async fillAndPublishArticle(title: string, about: string, content: string, tags: string[]): Promise {
   await this.page.getByPlaceholder(this.titleInput).fill(title);
   await this.page.getByPlaceholder(this.aboutInput).fill(about);
   await this.page.getByPlaceholder(this.contentInput).fill(content);
   await this.page.getByPlaceholder(this.tagInput).fill(tags.join(’ ’));
   await this.page.getByRole(“button”, this.publishButton).click();
  }
 }
```
---

### Перенос в тест
```
test(‘Создание статьи’, async ({ page }) => {
  const editorPage = new EditorPageClass(page)

await page.getByRole(‘link’, { name: ‘New Post’ }).click()

await editorPage.fillAndPublishArticle(
   ‘article title’,
   ‘about article’,
   ‘article content’,
   [‘article’]
  )

await expect(page.getByRole(‘heading’)).toContainText(‘article title’)
  await expect(page.getByRole(‘button’, { name: ‘Delete Article’ })).nth(1).toBeVisible()
 })
```