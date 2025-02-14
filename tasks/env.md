# Чистый код: настройка линтера и форматтера

## Цель
Отработать навыки настройки линтера / форматтера.

## Задание

Репозитории созданном в рамках предыдущего ДЗ создайте ветку `task/env`

```bash
git checkout -b task/env
```

Выполните ДЗ в ней. После отправьте пул-реквест на проверку.

**Вариант 1:**
* скопируйте содержимое ветки `master` из репозитория [otus-qajs](https://github.com/OTUS-QA-JS/otus-qajs/tree/master)
* выполните команду `npm ci` в корне проекта
* запустите команду `npm start`. Вы должны увидеть в консоли: `Hello, World!`
* измените в файле `src/main.js` строку `console.log(greet('World'))` на `console.log(greet("World"))`
* запустите линтер командой `npm run lint`
* убедитесь, что в файле `src/main.js` вы видите строку `console.log(greet('World'))`, а не `console.log(greet("World"))`

**Вариант 2:**
* инициализируете проект командой [npm init](https://docs.npmjs.com/cli/v8/commands/npm-init)
* установите [prettier](https://prettier.io/docs/install)
* создайте файл `src/main.js` с таким содержимым:
```js
const
    helloPrefix =
        "Hello, ";

function greet(name)
{
    return `${helloPrefix} ${name}!`
}
console.log(
    greet('World')
)
```
* запустите `prettier` командой `npx prettier . --write`
* посмотрите на файл `src/main.js`. Он должен быть отформатирован.
* добавьте скрипты (алиасы) для запуска `prettier` в `package.json`
Пример:
```json
{
  // ...
  "scripts": {
    "lint:ci": "prettier . --check",
    "lint": "prettier . --write"
  },
  // ...
}
```
* удалите содержимое файла `src/main.js` и после заново наполните его содержимым, выше по-тексту.
* запустите `npm run lint:ci`. Изучите вывод в консоль. После содержимое файла `src/main.js`
* запустите `npm run lint`. Изучите вывод в консоль. После содержимое файла `src/main.js`
* дальше это ДЗ выполнять не обязательно, но если дойдёте до конца, ваша жизнь станет лучше, а волосы шелковистыми. Есть и побочные эффекты: красные глаза, мокрая от пота футболка, новые знания. 

*** план, пишу для себя, чтобы не забыть, что не дописал***
TODO:
* написать про настройку линтера
* editorconfig
* vs code extensions
* CI????
* vs code dev containers?????
***КОНЕЦ ПЛАНА***


**Вариант 3:**
Для продвинутых
* инициализируйте проект (`npm init`)
* добавьте в проект линтер и / или форматтер по вашему выбору
* настройте их под себя
* в `package.json` добавьте скрипты (алиасы) для запуска линтера локально и в CI. Желательно `lint` и `lint:ci`. [Пример package.json](https://github.com/OTUS-QA-JS/otus-qajs/blob/master/package.json).

**Советы:**

*Совет 1:*

Если вы хотите настроить `prettier` под себя:
* откройте [prettier playground](https://prettier.io/playground)
* в левой колонке вставьте содержимое файла `src/main.js`
* поиграйтесь с настройками, которые предложит playground. После того как результат справа вас будет устраивать, нажмите кнопку `Copy config JSON`

---

*Совет 2:*
`Editorcofig` поможет вам иметь одинаковые настройки форматирования в разных редакторах. [Подробнее](https://editorconfig.org/).

---

**Совет 3:**
Для подсветок подсказок от `eslint` / `prettier` в `vs code` установите плагины:
* [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
* [Eslint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

---
## Критерии оценивания
