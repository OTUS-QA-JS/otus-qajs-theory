# Локаторы

## Содержание

1. [Назначение локаторов](#назначение-локаторов)
2. [Структура веб-страницы](#структура-веб-страницы)
3. [Типы локаторов](#типы-локаторов)
4. [CSS-селекторы](#css-селекторы)
5. [XPath-локаторы](#xpath-локаторы)
6. [Стабильность локаторов](#стабильность-локаторов)
7. [Лучшие практики](#лучшие-практики)
8. [Сравнение CSS и XPath](#сравнение-css-и-xpath)
9. [Примеры использования](#примеры-использования)

## Назначение локаторов

Локаторы представляют собой способы идентификации элементов на веб-странице для выполнения автоматизированного тестирования. Они служат связующим звеном между тестовым кодом и элементами пользовательского интерфейса.

Основные задачи локаторов:
- Поиск элементов на странице
- Взаимодействие с элементами (клики, ввод текста)
- Проверка состояния элементов

## Структура веб-страницы

Веб-страница представляет собой иерархическую структуру элементов HTML, организованную в виде дерева DOM (Document Object Model). Каждый элемент может содержать:

- Тег (tag)
- Атрибуты (id, class, name, data-*)
- Текстовое содержимое
- Дочерние элементы

## Типы локаторов

Существуют два основных типа локаторов:

### CSS-селекторы
- Основаны на синтаксисе каскадных таблиц стилей
- Быстрые в выполнении
- Хорошо поддерживаются браузерами
- Ограниченные возможности навигации по DOM

### XPath-локаторы
- Основаны на языке запросов XML Path Language
- Более медленные в выполнении
- Расширенные возможности навигации и поиска
- Поддержка функций и предикатов

## CSS-селекторы

### Основные типы CSS-селекторов

#### По ID
```css
#element-id
```

#### По классу
```css
.class-name
.multiple.classes
```

#### По тегу
```css
button
div
input
```

#### По атрибуту
```css
[name="username"]
[data-testid="submit-button"]
[type="email"]
```

#### Комбинированные селекторы
```css
/* Дочерний элемент */
.parent > .child

/* Потомок */
.ancestor .descendant

/* Соседний элемент */
.first + .next

/* Общие соседние элементы */
.first ~ .siblings
```

#### Псевдо-селекторы
```css
/* Первый элемент */
:first-child

/* Последний элемент */
:last-child

/* По порядковому номеру */
:nth-child(2)

/* Содержащий текст */
:contains("текст")
```

## XPath-локаторы

### Синтаксис XPath

#### Базовые пути
```xpath
// - поиск в любом месте документа
/  - поиск от корня документа
.  - текущий элемент
.. - родительский элемент
```

#### Поиск по атрибутам
```xpath
//button[@id='submit']
//input[@name='username']
//div[@class='container']
```

#### Поиск по тексту
```xpath
//button[text()='Войти']
//a[contains(text(), 'Подробнее')]
```

#### Предикаты и функции
```xpath
//div[position()=1]
//input[starts-with(@id, 'user')]
//span[string-length(text()) > 10]
```

#### Оси навигации
```xpath
//button/following-sibling::input
//div/ancestor::form
//table/descendant::td
```

## Стабильность локаторов

### Проблема нестабильных локаторов

Пример проблемной ситуации:
1. Создается страница с кнопкой `<button class="confirm-button">Подтвердить</button>`
2. Тест использует локатор `.confirm-button`
3. Разработчики переходят на Tailwind CSS
4. Класс меняется на `p-2 bg-red w-full`
5. Тест перестает работать

### Решения для обеспечения стабильности

#### Простое решение
Использование специальных атрибутов для тестирования:
```html
<button data-testid="confirm-button" class="p-2 bg-red w-full">
  Подтвердить
</button>
```

```css
[data-testid="confirm-button"]
```

#### Более сложное решение
Использование семантических локаторов и role-based селекторов:
```javascript
// Playwright
page.getByRole('button', { name: 'Подтвердить' })
page.getByText('Подтвердить')
page.getByLabel('Email')
```

## Лучшие практики

### Приоритет использования локаторов

1. **data-testid** - наивысший приоритет для автотестов
2. **id** - уникальные идентификаторы
3. **name** - для элементов форм
4. **role и aria-атрибуты** - для семантического поиска
5. **class** - стабильные классы компонентов
6. **tag + атрибуты** - комбинированный подход
7. **XPath по тексту** - наименьший приоритет

### Рекомендации

- Избегайте локаторов, зависящих от структуры DOM
- Не используйте индексы и позиции без крайней необходимости
- Предпочитайте читаемые и понятные локаторы
- Документируйте сложные локаторы
- Регулярно проверяйте актуальность локаторов

## Сравнение CSS и XPath

| Критерий | CSS | XPath |
|----------|-----|-------|
| Скорость выполнения | Быстрее | Медленнее |
| Читаемость | Высокая | Средняя |
| Возможности поиска | Ограниченные | Расширенные |
| Поиск по тексту | Ограниченный | Полная поддержка |
| Навигация по DOM | Базовая | Полная |
| Поддержка браузерами | Отличная | Хорошая |
| Сложность изучения | Низкая | Средняя |

## Примеры использования

### Поиск элементов формы

```javascript
// CSS
await page.locator('#email').fill('user@example.com');
await page.locator('[name="password"]').fill('secret');
await page.locator('button[type="submit"]').click();

// XPath
await page.locator('//input[@id="email"]').fill('user@example.com');
await page.locator('//input[@name="password"]').fill('secret');
await page.locator('//button[@type="submit"]').click();
```

### Поиск по тексту

```javascript
// CSS (ограниченно)
await page.locator('button:has-text("Войти")').click();

// XPath
await page.locator('//button[text()="Войти"]').click();
await page.locator('//a[contains(text(), "Подробнее")]').click();
```

### Работа со списками

```javascript
// CSS
await page.locator('ul.menu > li:first-child').click();
await page.locator('table tr:nth-child(2) td').first().textContent();

// XPath
await page.locator('//ul[@class="menu"]/li[1]').click();
await page.locator('//table/tr[2]/td[1]').textContent();
```

### Поиск по родительским элементам

```javascript
// CSS
await page.locator('.form-group:has(label:text("Email")) input').fill('test@test.com');

// XPath
await page.locator('//label[text()="Email"]/following-sibling::input').fill('test@test.com');
await page.locator('//input[@name="username"]/ancestor::form').screenshot();
```

### Использование data-testid

```html
<form data-testid="login-form">
  <input data-testid="username-input" type="text" name="username">
  <input data-testid="password-input" type="password" name="password">
  <button data-testid="submit-button" type="submit">Войти</button>
</form>
```

```javascript
await page.locator('[data-testid="username-input"]').fill('user');
await page.locator('[data-testid="password-input"]').fill('pass');
await page.locator('[data-testid="submit-button"]').click();
```

## Инструменты для работы с локаторами

### Браузерные инструменты разработчика
- Поиск элементов через Ctrl+F в Elements
- Копирование селекторов правой кнопкой мыши
- Проверка уникальности локаторов

### Специализированные расширения
- SelectorsHub для Chrome/Firefox
- ChroPath для XPath
- Playwright Inspector для отладки

### Команды консоли
```javascript
// Поиск CSS
$('button.primary')
$$('.item')

// Поиск XPath
$x('//button[text()="Click"]')
$x('//div[@class="container"]//span')
```

Правильный выбор и использование локаторов является основой надежного автоматизированного тестирования пользовательского интерфейса.
