# Чистый код: настройка линтера и форматтера

## Цель
Отработать навыки настройки линтера и форматтера для обеспечения единого стиля кода в проекте

## Задание
### Вводные.

- В репозитории, созданном в рамках предыдущего ДЗ, создайте ветку `task/env` .
```bash
git checkout -b task/env
```
- Выполните домашнее задание в этой ветке. После этого отправьте пулреквест на проверку.

### Вариант 1:
1. скопируйте содержимое ветки `master` из репозитория [otus-qajs](https://github.com/OTUS-QA-JS/otus-qajs/tree/master). Ссылка на архив репозитория: [https://github.com/OTUS-QA-JS/otus-qajs/archive/refs/heads/master.zip](https://github.com/OTUS-QA-JS/otus-qajs/archive/refs/heads/master.zip)
2. выполните команду `npm ci` в корне проекта
3. запустите команду `npm start`. Вы должны увидеть в консоли: `Hello, World!`
4. измените в файле `src/main.js` строку `console.log(greet('World'))` на `console.log(greet("World"))`
5. запустите линтер командой `npm run lint`
6. убедитесь, что в файле `src/main.js` вы видите строку `console.log(greet('World'))`, а не `console.log(greet("World"))`

### Вариант 2:
- **Часть 1. Prettier**
1. инициализируете проект командой [npm init](https://docs.npmjs.com/cli/v8/commands/npm-init)
2. установите [prettier](https://prettier.io/docs/install)
3. создайте файл `src/main.js` с таким содержимым:

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
4. запустите `prettier` командой `npx prettier . --write`
5. посмотрите на файл `src/main.js`. Он должен быть отформатирован
6. добавьте скрипты (алиасы) для запуска `prettier` в `package.json`

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
7. удалите содержимое файла `src/main.js` и после заново наполните его содержимым, **ниже по-тексту**
8. создайте файл `.prettierrc` с таким содержимым:
 ```json
{
    "semi": false,
    "singleQuote": true,
    "printWidth": 120,
    "trailingComma": "none",
    "arrowParens": "avoid"
}
```
9. создайте файл: `.prettierignore` с таким содержимым:

```
reports
build
coverage
```
Все пути до папок / файлов выше будут игнорироваться `prettier`

10. вы можете настроить `prettier` под ваши предпочтения (смотрите рекомендацию №1 ниже)
11. запустите `npm run lint:ci`. Изучите вывод в консоль. После содержимое файла `src/main.js`
12. запустите `npm run lint`. Изучите вывод в консоль. После содержимое файла `src/main.js`
13. создайте файл `.gitignore` с таким содержимым:
```
node_modules
```

Файл `.gitingore` сгенерировать под ваше окружение на сайте [gitignore.io](https://www.toptal.com/developers/gitignore)

Пример: Windows, VS Code, NodeJS -- [https://www.toptal.com/developers/gitignore/api/windows,node,visualstudiocode](https://www.toptal.com/developers/gitignore/api/windows,node,visualstudiocode)

Дальше это ДЗ выполнять не обязательно, но если дойдёте до конца, ваша жизнь станет лучше, а волосы шелковистыми 😉
Есть и побочные эффекты: красные глаза, мокрая от пота футболка, новые знания 💪🏻

- **Часть 2. Eslint**
1. создадим файл конфигурации `eslint` командой `npm init @eslint/config@latest`
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

? Where does your code run? ... (Press <space> to select, <a> to toggle all, <i> to invert selection)
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

Вы должны увидеть в конце
Successfully created C:\Projects\otus\qa\temp\eslint.config.mjs file.
```
2. Теперь, мы можем запустить проверку `code-style` используя команду:
```bash
npx eslint --fix
```

- **Часть 3. Eslint + Prettier**
1. Теперь мы отдельно можем запускать `prettier`, другой командой `eslint`, но мы можем их объеденить
2. Сделаем так, чтобы при запуске `eslint` у нас одновременно запускался и `prettier`
3. Нам понадобится [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)

```bash
npm install --save-dev eslint-plugin-prettier eslint-config-prettier
```

4. а после обновить конфиг `eslint.config.mjs`
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

5. Теперь при запускеп `eslint`, будет запускаться и `prettier`
6. Замените скрипты (алиасы) для запуска `prettier` в `package.json` на `eslint`.
   [Пример](https://github.com/OTUS-QA-JS/otus-qajs/blob/master/package.json) 👈🏻
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


### Вариант 2:
**Для продвинутых** 🤘🏻
1. инициализируйте проект (`npm init`)
2. добавьте в проект линтер и / или форматтер по вашему выбору
3. настройте их под себя
4. в `package.json` добавьте скрипты (алиасы) для запуска линтера локально и в CI. Желательно `lint` и `lint:ci`. [Пример package.json](https://github.com/OTUS-QA-JS/otus-qajs/blob/master/package.json)  👈🏻

💡**Рекомендации:**

1. Если вы хотите настроить `prettier` под себя:
* откройте [prettier playground](https://prettier.io/playground)
* в левой колонке вставьте содержимое файла `src/main.js`
* поиграйтесь с настройками, которые предложит playground. После того как результат справа вас будет устраивать, нажмите кнопку `Copy config JSON`

2. `Editorcofig` поможет вам иметь одинаковые настройки форматирования в разных редакторах. [Подробнее](https://editorconfig.org/) 👈🏻
3. Для подсветок подсказок от `eslint` / `prettier` в `vs code` установите плагины:
* [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
* [Eslint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)


---
## Критерии оценивания
1. Линтер или форматтер настроен и работает корректно.
2. Скрипты в package.json выполняют свои функции.
3. Код соответствует стандартам, установленным линтером и форматтером.