# Непрерывная интеграция с GitHub Actions

## Содержание

1. [Введение](#введение)
2. [Постановка задачи](#постановка-задачи)
3. [Концепция вебхуков](#концепция-вебхуков)
4. [Основы CI/CD](#основы-cicd)
5. [GitHub Actions](#github-actions)
6. [Конфигурация YAML](#конфигурация-yaml)
7. [Интеграция с Telegram](#интеграция-с-telegram)
8. [Публикация на GitHub Pages](#публикация-на-github-pages)
9. [Практические примеры](#практические-примеры)
10. [Устранение неполадок](#устранение-неполадок)
11. [Лучшие практики](#лучшие-практики)
12. [Материалы для изучения](#материалы-для-изучения)

## Введение

Непрерывная интеграция (Continuous Integration, CI) представляет собой ключевую практику современной разработки программного обеспечения. GitHub Actions предоставляет встроенные возможности для автоматизации процессов разработки, тестирования и развертывания непосредственно в экосистеме GitHub.

## Постановка задачи

Основные цели изучения непрерывной интеграции:

- Настройка автоматического запуска тестов и проверок при изменениях в репозитории
- Создание системы уведомлений о результатах выполнения пайплайна
- Освоение базовых элементов CI на платформе GitHub Actions
- Внедрение автоматизации для упрощения процесса разработки

## Концепция вебхуков

Вебхуки представляют собой механизм автоматического уведомления о событиях в одном сервисе с возможностью реагирования в другом. При наступлении определенного события (например, push в GitHub) система автоматически отправляет HTTP-запрос на заданный адрес.

### Применение вебхуков

Типичные сценарии использования:
- Отправка уведомлений в мессенджеры (Telegram, Slack)
- Инициирование процесса сборки приложения
- Ведение журнала активности
- Запуск внешних API и сервисов

## Основы CI/CD

### Определения

**Continuous Integration (CI)** - практика автоматической сборки и тестирования кода после каждого изменения в репозитории.

**Continuous Delivery/Deployment (CD)** - автоматическая доставка или развертывание изменений до конечных пользователей.

### Преимущества CI/CD

- Быстрое обнаружение и исправление ошибок
- Снижение количества дефектов в продуктивной среде
- Стандартизация процесса выпуска новых функций
- Повышение качества и стабильности программного продукта

### Популярные инструменты CI/CD

Основные системы непрерывной интеграции:
- Jenkins
- TeamCity
- GitLab CI
- Travis CI
- CircleCI
- GitHub Actions

## GitHub Actions

GitHub Actions представляет собой встроенную платформу CI/CD, интегрированную непосредственно в GitHub без необходимости использования внешних сервисов.

### Основные компоненты

**Workflow** - набор автоматизированных задач, описанных в YAML-файле, расположенном в директории `.github/workflows/`

**Event** - событие-триггер, инициирующее выполнение workflow (push, pull_request, schedule и другие)

**Job** - группа шагов, выполняющихся последовательно на одной виртуальной машине

**Step** - отдельное действие: команда shell или готовый action

**Runner** - виртуальная машина, предоставляющая среду выполнения

**Action** - переиспользуемый компонент, доступный в GitHub Marketplace или созданный самостоятельно

### Базовый пример workflow

```yml
# .github/workflows/test.yml
name: test
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22.17.0
          cache: 'npm'
      - run: npm ci
      - run: npm run test:ci
```

## Конфигурация YAML

YAML (YAML Ain't Markup Language) представляет собой человеко-читаемый формат сериализации данных, широко используемый для конфигурационных файлов.

### Основные элементы синтаксиса

- Пары ключ-значение: `key: value`
- Вложенность определяется отступами
- Списки обозначаются символом `-`
- Отступы критически важны для корректной интерпретации

### Области применения

YAML используется в:
- GitHub Actions
- Docker Compose
- Kubernetes
- Ansible
- Многих других системах конфигурации

## Интеграция с Telegram

### Создание бота

Процедура создания Telegram-бота:

1. Найти `@BotFather` в Telegram
2. Создать нового бота и получить токен аутентификации
3. Добавить бота в целевую группу или чат
4. Получить идентификатор чата (chat_id)

### Отправка уведомлений

Пример HTTP-запроса для отправки сообщения:

```bash
curl --request POST \
  --url https://api.telegram.org/bot${TOKEN}/sendMessage \
  --header 'Content-Type: application/json' \
  --data '{
    "chat_id": "${CHAT_ID}",
    "text": "Тесты выполнены успешно"
  }'
```

### Конфигурация в GitHub Actions

```yml
- run: |
    curl --request POST \
         --url https://api.telegram.org/bot${{secrets.TELEGRAM_TOKEN}}/sendMessage \
         --header 'Content-Type: application/json' \
         --data '{"chat_id": "${{secrets.TELEGRAM_CHAT_ID}}", "text": "Результат сборки: успешно"}'
```

## Публикация на GitHub Pages

### Настройка репозитория

Для активации GitHub Pages через Actions:

1. Перейти в Settings репозитория
2. Выбрать раздел Pages
3. В разделе "Build and deployment" выбрать источник "GitHub Actions"
4. При необходимости публикации из любой ветки: Settings → Environments → github-pages → "No restriction"

### Конфигурация публикации

```yml
test:
  environment:
    name: github-pages
    url: ${{ steps.deployment.outputs.page_url }}
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: 22.17.0
        cache: 'npm'
    - run: npm ci
    - run: npm test
    - name: Setup Pages
      uses: actions/configure-pages@v4
    - name: Upload artifact
      uses: actions/upload-pages-artifact@v3
      with:
        path: 'reports/test-report'
    - name: Deploy to GitHub Pages
      id: deployment
      uses: actions/deploy-pages@v4
```

## Практические примеры

### Комплексный workflow с тестированием и уведомлениями

```yml
name: CI Pipeline
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22.17.0
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build

  notification:
    if: always()
    needs: [test]
    runs-on: ubuntu-latest
    steps:
      - run: |
          STATUS="${{ needs.test.result }}"
          MESSAGE="Сборка ${{ github.run_number }}: $STATUS"
          curl --request POST \
               --url https://api.telegram.org/bot${{secrets.TELEGRAM_TOKEN}}/sendMessage \
               --header 'Content-Type: application/json' \
               --data "{\"chat_id\": \"${{secrets.TELEGRAM_CHAT_ID}}\", \"text\": \"$MESSAGE\"}"
```

### Матричное тестирование

```yml
test:
  strategy:
    matrix:
      node-version: [18, 20, 22]
      os: [ubuntu-latest, windows-latest, macos-latest]
  runs-on: ${{ matrix.os }}
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
    - run: npm ci
    - run: npm test
```

## Устранение неполадок

### Проблемы с GitHub Pages

**Симптомы**: Отчет не публикуется на GitHub Pages

**Решения**:
- Проверить активацию GitHub Actions в настройках Pages
- Убедиться в корректности пути к отчету в `upload-pages-artifact`
- Проверить разрешения workflow в Settings → Actions → General

### Различия между локальной и CI-средой

**Симптомы**: Тесты успешны локально, но падают в CI

**Причины и решения**:
- Различия в версиях Node.js: зафиксировать версию в workflow
- Отсутствие переменных окружения: использовать GitHub Secrets
- Различия в операционных системах: учесть особенности Ubuntu в CI

### Проблемы с Telegram-уведомлениями

**Симптомы**: Уведомления не доставляются

**Решения**:
- Проверить корректность токена и chat_id в Secrets
- Убедиться в наличии прав у бота на отправку сообщений
- Проверить синтаксис JSON в запросе

### Проблемы с запуском workflow

**Симптомы**: Workflow не активируется

**Решения**:
- Проверить синтаксис YAML (критически важны отступы)
- Убедиться в корректном расположении файла в `.github/workflows/`
- Проверить триггеры в секции `on:`

## Лучшие практики

### Структурирование workflow

**Модульность**: Разделение различных задач на отдельные jobs (тестирование, линтинг, развертывание)

**Зависимости**: Использование `needs` для определения последовательности выполнения jobs

**Группировка**: Объединение связанных действий в рамках одного job

### Оптимизация производительности

**Кеширование**: Использование кеша для node_modules через `cache: 'npm'`

**Параллелизация**: Запуск независимых jobs параллельно

**Матричные стратегии**: Тестирование на различных версиях и платформах

### Безопасность

**Секреты**: Использование GitHub Secrets для токенов и конфиденциальных данных

**Минимальные права**: Предоставление workflow минимально необходимых разрешений

**Аудит зависимостей**: Регулярная проверка используемых actions на безопасность

### Мониторинг и уведомления

**Статус сборки**: Настройка уведомлений о результатах выполнения

**Критичные уведомления**: Использование `if: always()` для важных оповещений

**Логирование**: Подробное логирование для упрощения отладки

### Примеры конфигурации переменных окружения

```yml
jobs:
  test:
    env:
      NODE_ENV: test
      DATABASE_URL: ${{ secrets.DATABASE_URL }}
    steps:
      - run: echo "Среда: $NODE_ENV"
      - run: npm test
```

## Материалы для изучения

### Официальная документация

- [Understanding GitHub Actions](https://docs.github.com/en/actions/learn-github-actions) - полное руководство по GitHub Actions
- [Deploying with GitHub Actions](https://docs.github.com/en/actions/concepts/use-cases/deploying-with-github-actions) - развертывание с помощью Actions

### Дополнительные ресурсы

- [CI/CD концепции и практики](https://habr.com/ru/articles/448980/) - общие принципы непрерывной интеграции
- [YAML синтаксис за 5 минут](https://tproger.ru/translations/yaml-cheatsheet/) - быстрое введение в YAML
- [Безопасность GitHub Actions](https://levelup.gitconnected.com/structuring-github-actions-safely-24260f74bfc1) - рекомендации по безопасности

### Сообщество и примеры

- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) - готовые actions для использования
- [Awesome Actions](https://github.com/sdras/awesome-actions) - курированный список полезных actions

## Заключение

Непрерывная интеграция представляет собой инструмент для достижения целей качественной и эффективной разработки, а не самоцель. Даже базовая автоматизация значительно повышает стабильность процессов разработки и освобождает время разработчиков для решения более важных задач.

Успешное внедрение CI/CD требует понимания специфики проекта, потребностей команды и постепенного наращивания сложности автоматизации. GitHub Actions предоставляет мощную и гибкую платформу для реализации различных сценариев непрерывной интеграции и доставки.