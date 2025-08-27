# CI/CD с GitLab CI

## Содержание
- [Что такое CI/CD и GitLab CI?](#что-такое-cicd-и-gitlab-ci)
- [Основные компоненты GitLab CI](#основные-компоненты-gitlab-ci)
- [Конфигурация: `.gitlab-ci.yml`](#конфигурация-gitlab-ciyml)
- [Управление выполнением задач](#управление-выполнением-задач)
- [Работа с артефактами (Artifacts)](#работа-с-артефактами-artifacts)
- [Сборка Docker-образов в CI](#сборка-docker-образов-в-ci)
- [Полезные ресурсы](#полезные-ресурсы)

## Что такое CI/CD и GitLab CI?

**CI/CD (Continuous Integration/Continuous Delivery/Deployment)** — это практика автоматизации этапов разработки программного обеспечения. CI/CD помогает чаще и надежнее развертывать изменения.

-   **Continuous Integration (CI)**: Процесс автоматической сборки и тестирования кода при каждом изменении, внесенном в репозиторий.
-   **Continuous Delivery (CD)**: Автоматическая доставка изменений в окружение (например, тестовое или продакшн) после успешного прохождения CI.

**GitLab CI/CD** — это мощный инструмент, встроенный в GitLab, который позволяет полностью автоматизировать жизненный цикл разработки прямо из репозитория.

## Основные компоненты GitLab CI

GitLab CI использует концепцию пайплайнов для определения и выполнения CI/CD процессов.

-   **Пайплайн (Pipeline)**: Весь набор процессов, который запускается при определенном событии (например, push в ветку). Состоит из одного или нескольких этапов.
-   **Этап (Stage)**: Группа задач, которые выполняются на определенном шаге пайплайна. Задачи внутри одного этапа могут выполняться параллельно. Этапы выполняются последовательно (например, этап `build` выполняется перед этапом `test`).
-   **Задача (Job)**: Минимальная единица работы. Определяет, ЧТО нужно сделать (например, запустить тесты, собрать приложение). Каждая задача выполняется на **раннере** (Runner) — агенте, который исполняет код.

## Конфигурация: `.gitlab-ci.yml`

Вся конфигурация пайплайна описывается в файле `.gitlab-ci.yml` в корне репозитория с использованием синтаксиса YAML.

### Базовая структура

```yaml
# 1. Определение последовательности этапов
stages:
  - build
  - test
  - deploy

# 2. Определение задачи (job)
run_tests:
  # 3. Указание, к какому этапу относится задача
  stage: test

  # 4. Указание Docker-образа, в котором будет выполняться задача
  image: node:20

  # 5. Скрипт, который будет выполнен
  script:
    - echo "Установка зависимостей..."
    - npm ci
    - echo "Запуск тестов..."
    - npm test
```

## Управление выполнением задач

### `rules`: Условия запуска задач
Директива `rules` позволяет гибко настраивать условия, при которых задача будет добавлена в пайплайн.

```yaml
deploy_to_prod:
  stage: deploy
  script:
    - echo "Развертывание на продакшн..."
  rules:
    # Запускать эту задачу только при пуше в основную ветку
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

### `when: manual`: Ручной запуск
Иногда требуется, чтобы критически важные задачи (например, деплой в продакшн) запускались только вручную после подтверждения пользователем. Это делается с помощью `when: manual`.

```yaml
deploy_to_prod:
  stage: deploy
  script:
    - echo "Развертывание на продакшн..."
  when: manual # Задача будет ждать ручного запуска в UI GitLab
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

## Работа с артефактами (Artifacts)

**Артефакты** — это файлы, которые создаются в процессе выполнения задачи (например, отчеты о тестах, скомпилированные файлы). GitLab может сохранять эти файлы и передавать их между этапами пайплайна или делать доступными для скачивания.

```yaml
run_tests:
  stage: test
  image: node:20
  script:
    - npm ci
    - npm test
  artifacts:
    # Указываем пути к файлам или папкам, которые нужно сохранить
    paths:
      - reports/allure-report/
    # Указываем, как долго хранить артефакты
    expire_in: 1 week
```

## Сборка Docker-образов в CI

GitLab CI позволяет автоматизировать сборку и публикацию Docker-образов в **Container Registry** — встроенном в GitLab реестре Docker-образов.

### Docker-in-Docker (dind)
Для выполнения Docker-команд внутри CI-задачи используется подход "Docker-in-Docker". Задача запускается в образе `docker`, а в качестве сервиса подключается `docker:dind` (Docker-in-Docker daemon).

```yaml
build_image:
  stage: build
  # Используем официальный образ Docker
  image: docker:20.10.16
  # Подключаем сервис Docker-in-Docker
  services:
    - docker:20.10.16-dind
  script:
    # GitLab предоставляет предопределенные переменные для доступа к Registry
    # $CI_REGISTRY_USER, $CI_REGISTRY_PASSWORD, $CI_REGISTRY
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    
    # Собираем и тегируем образ
    # $CI_REGISTRY_IMAGE - URL образа в реестре проекта
    # $CI_COMMIT_REF_SLUG - имя ветки, безопасное для URL
    - docker build -t $CI_REGISTRY_IMAGE:latest -t $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG .
    
    # Публикуем образы в Container Registry
    - docker push $CI_REGISTRY_IMAGE:latest
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
```

## Полезные ресурсы
-   **Martin Fowler**: [Continuous Integration](https://martinfowler.com/articles/continuousIntegration.html)
-   **Дока**: [GitHub Actions](https://doka.guide/tools/github-actions/) (многие концепции схожи с GitLab CI)
-   **Habr**: [Что такое CI (Continuous Integration)](https://habr.com/ru/companies/southbridge/articles/327240/)
-   **Tproger**: [YAML за 5 минут](https://tproger.ru/translations/yaml-in-5-minutes/)