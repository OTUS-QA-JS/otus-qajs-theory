# Вопросы и ответы с вебинара QA DD.MM.YYYY

## Вопрос 1.

### В:
У меня возникли сложности с наполнением файла tsconfig. Видела содержимое файла в эталонном репозитории.
Но очень интересуют отсутствующий там параметр verbatiumModuleSyntax, а также взаимосвязанные параметры "module", "target", "moduleResolution". Какие значения для них выбрать и почему.

### О:

Коротки ответ:
verbatimModuleSyntax -- обязывает указывать `type` если импортируем только тип

module

target

moduleResolution

target — версия JavaScript, в которую компилируется код:

ES2022 — современный стандарт с поддержкой новых функций
ES2020 — более консервативный выбор для совместимости

module — система модулей:

ESNext — для современных ES модулей (import/export)
CommonJS — для Node.js проектов с require/module.exports

moduleResolution — как TypeScript разрешает импорты:

node — стандартное разрешение для Node.js
bundler — для современных бандлеров (Vite, Webpack)

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "CommonJS", 
    "moduleResolution": "node"
  }
}
```

Подробный ответ:
