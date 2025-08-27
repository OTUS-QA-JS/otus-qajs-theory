# Настройка окружения

## Содержание

- [Основные требования](#основные-требования)
- [Рекомендации по установке](#рекомендации-по-установке)
- [JavaScript и окружения](#javascript-и-окружения)
- [Интерфейсы](#интерфейсы)
- [Основные команды терминала](#основные-команды-терминала)
- [Полезные алиасы путей](#полезные-алиасы-путей)
- [Работа с проектами](#работа-с-проектами)
- [Инструменты качества кода](#инструменты-качества-кода)
- [Порядок форматирования](#порядок-форматирования)
- [AI-инструменты](#ai-инструменты)
- [Итоговый набор инструментов](#итоговый-набор-инструментов)

## Основные требования

### Обязательная установка

[GIT](https://git-scm.com/downloads) - система контроля версий
[NodeJS LTS](https://nodejs.org/en/download) - среда выполнения JavaScript (если нужно несколько версий nodejs, то устанавливайте nvm)

### Редакторы кода

Любой редактор кода:
- [Visual Studio Code](https://code.visualstudio.com/) – бесплатный
- [WebStorm](https://www.jetbrains.com/webstorm/) – бесплатный для некоммерческого использования

### Опционально

[nvm-sh](https://github.com/nvm-sh/nvm) или [nvm-windows](https://github.com/coreybutler/nvm-windows) - менеджер версий Node.js, если есть необходимость переключаться между разными версиями

## Рекомендации по установке

### Windows

**VS Code**
- Включить "добавить в PATH" для возможности открыть папку из консоли командой `code -n .`

**GIT**
- Изменить редактор по-умолчанию на VS Code или используемый редактор
- Настроить окончания строк (рекомендуется Unix-стиль)

### Дополнительные инструменты

**Терминал**
- Windows: Windows Terminal, Cmder
- macOS: iTerm2
- Linux: terminator
- Альтернатива: встроенный в редактор кода терминал

**Настройки терминала**
- Настроить домашнюю директорию для открытия папки с проектами
- Для macOS/Linux: рассмотреть oh-my-zsh

## JavaScript и окружения

JavaScript работает в различных окружениях:
- **Браузер** - для веб-приложений
- **Node.js** - для серверных приложений и инструментов разработки

Node.js включает NPM (менеджер пакетов) для управления зависимостями проекта.

## Интерфейсы

- **GUI** (Graphical User Interface) - графический интерфейс, управление мышкой
- **CLI** (Command Line Interface) - интерфейс командной строки, управление через текст

## Основные команды терминала

```bash
cd путь/к/папке          # перейти в папку
mkdir путь/к/папке       # создать папку
ls путь/к/папке          # список файлов в папке
clear                    # очистить вывод
cp старый/файл новый/файл # копировать файл
cp -r папка новая/папка   # копировать папку

# Открытие проводника
ii путь/к/папке          # Windows PowerShell
explorer.exe .           # Windows
open путь/к/папке        # macOS

code -n путь/к/папке     # открыть папку в VS Code
```

## Полезные алиасы путей

- `~` - домашняя директория пользователя
- `/` - корневая папка (в Windows по-умолчанию C:\)
- `.` - текущая папка

## Работа с проектами

### Создание проекта

```bash
mkdir ~/Projects/hello-world
cd ~/Projects/hello-world
npm init -y                 # создать package.json
code -n .                   # открыть в VS Code
```

### package.json

Центральный файл проекта, содержащий:
- Метаданные проекта
- Список зависимостей (dependencies/devDependencies)
- Скрипты для автоматизации (scripts)

Некоторые скрипты можно запускать сокращенно:
- `npm start` вместо `npm run start`
- `npm test` вместо `npm run test`

## Инструменты качества кода

### Prettier - форматирование кода

```bash
npm install --save-dev --save-exact prettier
node --eval "fs.writeFileSync('.prettierrc','{}\n')"
```

Конфигурация в `.prettierrc`:
```json
{
  "semi": false,
  "singleQuote": true,
  "arrowParens": "avoid"
}
```

Игнорирование файлов в `.prettierignore`:
```
reports
build
node_modules
```

Скрипты в package.json:
```json
{
  "scripts": {
    "format:ci": "prettier . --check",
    "format": "prettier . --write"
  }
}
```

### ESLint - статический анализ кода

Установка и настройка:
```bash
npm init @eslint/config@latest
npm install --save-dev eslint-config-prettier
```

Конфигурация для совместимости с Prettier.

Скрипты в package.json:
```json
{
  "scripts": {
    "lint:ci": "eslint .",
    "lint": "eslint . --fix"
  }
}
```

### EditorConfig - настройки редактора

Файл `.editorconfig`:
```ini
root = true

[*]
end_of_line = lf
insert_final_newline = true

[*.{js,ts}]
charset = utf-8
indent_style = space
indent_size = 2
```

### VS Code настройки

Включить отображение пробелов и настроить Prettier как форматер по умолчанию:
```json
{
  "editor.renderWhitespace": "all",
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "files.autoSave": "afterDelay"
}
```

## Порядок форматирования

1. `npm run format` - исправить форматирование Prettier
2. `npm run lint` - исправить ошибки ESLint

## AI-инструменты

**Редакторы с AI:**
- [Giga Code](https://gitverse.ru/features/gigacode)
- [Cursor](https://www.cursor.com)
- [Windsurf](https://codeium.com/windsurf)

**Расширения:**
- [Codeium](https://codeium.com/)

## Итоговый набор инструментов

- **git** - система контроля версий
- **eslint** - контроль code-style
- **prettier** - автоформатирование
- **editorconfig** - настройки текстовых редакторов

---

**Полезные ссылки:**
- [Biome](https://biomejs.dev/) - быстрый форматер для JavaScript, TypeScript, JSX, JSON, CSS и GraphQL (совместимость с Prettier 97%)