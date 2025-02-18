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

### Часть 1. Prettier
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

### Часть 2. Eslint
* создадим файл конфигурации `eslint` командой `npm init @eslint/config@latest`
```
? How would you like to use ESLint? ..
  To check syntax only
> To check syntax and find problems

? What type of modules does your project use? ... 
> JavaScript modules (import/export)
  CommonJS (require/exports)
  None of these

√ What type of modules does your project use? · esm
? Which framework does your project use? ... 
  React
  Vue.js
> None of these

? Does your project use TypeScript? ... 
> No
  Yes

? Where does your code run? ...  (Press <space> to select, <a> to toggle all, <i> to invert selection)
√ Browser
√ Node

The config that you've selected requires the following dependencies:

eslint, globals, @eslint/js
? Would you like to install them now? » No / Yes  
> Yes

? Which package manager do you want to use? ... 
> npm
  yarn
  pnpm
  bun

# Вы должны увидеть в конце
Successfully created C:\Projects\otus\qa\temp\eslint.config.mjs file.
```

Теперь, мы можем запустить проверку `code-style` используя команду:
```bash
npx eslint --fix
```

### Часть 3. Eslint + Prettier
Теперь мы отдельно можем запускать `prettier`, другой командой `eslint`, но мы можем их объеденить.
Сделаем так, чтобы при запуске `eslint` у нас одновременно запускался и `prettier`.
Нам понадобится [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)

```bash
npm install --save-dev eslint-plugin-prettier eslint-config-prettier
```

А после обновить конфиг `eslint.config.mjs`
```js
import globals from 'globals'
import pluginJs from '@eslint/js'
import eslintPluginPrettierRecommended from 'eslint-plugin-prettier/recommended'

export default [
  { languageOptions: { globals: { ...globals.browser, ...globals.node } } },
  pluginJs.configs.recommended,
  eslintPluginPrettierRecommended
]
```

Теперь при запускеп `eslint`, будет запускаться и `prettier`

Замените скрипты (алиасы) для запуска `prettier` в `package.json` на `eslint`
[Пример](https://github.com/OTUS-QA-JS/otus-qajs/blob/master/package.json):
```json
{
  // ...
  "scripts": {
      "lint:ci": "eslint .",
      "lint": "eslint . --fix"
  },
  // ...
}
```

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
