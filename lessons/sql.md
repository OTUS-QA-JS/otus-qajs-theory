# Основы SQL и работа с базами данных в Node.js

## Содержание
- [Подготовка окружения с Docker Compose](#подготовка-окружения-с-docker-compose)
- [Работа с базой данных](#работа-с-базой-данных)
- [Основы SQL: Команда `SELECT`](#основы-sql-команда-select)
- [Работа с PostgreSQL из Node.js](#работа-с-postgresql-из-nodejs)
- [Полезные ресурсы](#полезные-ресурсы)

## Подготовка окружения с Docker Compose

Для локальной разработки и тестирования удобно поднимать базу данных в Docker-контейнере. **Docker Compose** — это инструмент, который позволяет определять и запускать многоконтейнерные приложения с помощью одного конфигурационного файла.

### Конфигурация `docker-compose.yml`
Создайте в корне проекта файл `docker-compose.yml` со следующим содержимым для запуска контейнера с PostgreSQL:

```yaml
services:
  db:
    image: postgres:16
    restart: unless-stopped
    ports:
      # Локальный порт : порт в контейнере
      - "65432:5432"
    volumes:
      # Сохраняет данные БД между перезапусками
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: demo
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: demo

volumes:
  postgres_data:
```

### Запуск и управление контейнером

-   **Запустить контейнер в фоновом режиме**:
    ```bash
    docker compose up -d
    ```
-   **Посмотреть логи контейнера**:
    ```bash
    docker compose logs -f db
    ```
-   **Остановить контейнер**:
    ```bash
    docker compose down
    ```
-   **Остановить контейнер и удалить данные** (включая базу данных):
    ```bash
    docker compose down -v
    ```

### Загрузка демонстрационной базы данных
Для практики можно использовать демонстрационную базу данных от Postgres Pro. Скачайте дамп и поместите его в папку `backup/postgres` в вашем проекте. Затем выполните команду, чтобы восстановить его в контейнере:

```bash
docker compose exec db psql -U demo -d demo -f /path/to/your/dump.sql
```

## Работа с базой данных

### Инструменты для подключения
-   **Консольные клиенты**: `psql` — стандартный клиент для PostgreSQL.
-   **Графические клиенты (GUI)**: DBeaver (бесплатный), DataGrip (платный), pgAdmin.

### Подключение через `psql`
Можно подключиться к базе данных внутри контейнера, не устанавливая `psql` на локальную машину:

```bash
docker compose exec -it db psql -U demo
```

**Основные команды `psql`:**
-   `\l` — посмотреть список баз данных.
-   `\c <db_name>` — подключиться к другой базе данных.
-   `\dt` — посмотреть список таблиц в текущей базе данных.
-   `\q` — выйти из `psql`.

## Основы SQL: Команда `SELECT`

Оператор `SELECT` используется для извлечения данных из одной или нескольких таблиц.

-   **Выборка всех столбцов** из таблицы `airports`:
    ```sql
    SELECT * FROM airports;
    ```
-   **Выборка конкретных столбцов**:
    ```sql
    SELECT airport_code, airport_name, city FROM airports;
    ```
-   **Фильтрация данных с помощью `WHERE`**:
    ```sql
    SELECT * FROM airports WHERE city = 'Москва';
    ```
-   **Подсчет количества строк с помощью `COUNT`**:
    ```sql
    SELECT count(*) FROM airports WHERE city = 'Москва';
    ```
-   **Сортировка (`ORDER BY`) и ограничение (`LIMIT`) выборки**:
    ```sql
    -- Выбрать 5 аэропортов, отсортированных по названию города
    SELECT * FROM airports ORDER BY city LIMIT 5;
    ```

## Работа с PostgreSQL из Node.js

### Установка и настройка клиента
Для взаимодействия с PostgreSQL из Node.js используется библиотека `pg`.

1.  **Установка**:
    ```bash
    npm install pg
    ```

2.  **Настройка подключения**:
    Создайте файл для конфигурации клиента, который будет считывать параметры из переменных окружения.

    ```javascript
    // db-client.js
    import 'dotenv/config';
    import pkg from 'pg';
    const { Client } = pkg;

    const config = {
      host: process.env.POSTGRES_HOST ?? 'localhost',
      port: process.env.POSTGRES_PORT ? parseInt(process.env.POSTGRES_PORT, 10) : 65432,
      database: process.env.POSTGRES_DB ?? 'demo',
      user: process.env.POSTGRES_USER ?? 'demo',
      password: process.env.POSTGRES_PASSWORD ?? 'secret',
    };

    export const createClient = () => new Client(config);
    ```

### Выполнение запросов
Основной цикл работы с базой данных: подключение, выполнение запроса, отключение.

```javascript
import { createClient } from './db-client.js';

const client = createClient();
await client.connect();

const response = await client.query('SELECT * FROM airports LIMIT 2;');
console.log(response.rows); // Результаты запроса находятся в response.rows

await client.end();
```

### Параметризованные запросы
Для безопасной передачи данных в запрос (во избежание SQL-инъекций) следует использовать параметризацию.

```javascript
const city = 'Москва';
const query = {
  text: 'SELECT * FROM airports WHERE city = $1;', // $1 - это плейсхолдер
  values: [city], // Значения для плейсхолдеров передаются в массиве
};

const result = await client.query(query);
console.log(result.rows);
```

### Поиск по шаблону: `LIKE`
Оператор `LIKE` используется для поиска строк, соответствующих определенному шаблону. Символ `%` означает любую последовательность символов.

```javascript
const getAirportsByCityPattern = async (cityPattern) => {
  const query = {
    text: 'SELECT airport_name, city FROM airports WHERE city LIKE $1;',
    values: [`%${cityPattern}`], // Найти города, название которых заканчивается на cityPattern
  };
  const result = await client.query(query);
  return result.rows;
};

const airports = await getAirportsByCityPattern('бург');
console.log(airports);
```

## Полезные ресурсы
-   **Postgres: первое знакомство**: [Книга от Postgres Pro](https://edu.postgrespro.ru/sql_primer.pdf)
-   **Демонстрационная база данных**: [Описание и файлы для скачивания](https://postgrespro.ru/education/demodb)
-   **Learn SQL in Y minutes**: [Быстрый справочник по SQL](https://learnxinyminutes.com/docs/sql/)