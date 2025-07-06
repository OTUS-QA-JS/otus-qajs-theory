# Библиотеки для тестирования API

---
Полезные ссылки:
* [PactumJS](Free & OpenSource REST API Testing Tool for all levels in a Test Pyramid)
---

## Подготовка

Перед началом тестирования API:
*	добавим конфиг,
*	добавим фикстуры,
*	контроллеры / сервисы для API — по ходу.
---
## Конфиг

Конфиг — это файл с данными, которые зависят от окружения (dev, stage, prod). После запуска тестов его нельзя менять.

Пример простого конфига:
```
const config = {
  baseURL: ‘https://…’,
  userId:  ‘user_id’,
  username: ‘user_name’,
  password: ‘password’
 }
 export default config
```
### Замороженный конфиг

Используем dotenv + Object.freeze, чтобы сделать конфиг неизменяемым:
```
import ‘dotenv/config’
 const config = {
  baseURL:  process.env.TEST_BASE_API_URL,
  userId:   process.env.TEST_USER_ID,
  username: process.env.TEST_USERNAME,
  password: process.env.TEST_PASSWORD,
 }
 export default Object.freeze(config)
```
Пример:
```
const config = { username: ‘correct’ }
 config = { username: ‘other’ }  // ошибка
 config.username = ‘other’
 console.log(config.username)  // other
```
Чтобы защититься от этого:
```
const config = Object.freeze({
  username: ‘correct’,
  admin: {
   username: ‘admin’
  }
 })
 config.username = ‘other’
 config.admin.username = ‘user’
 console.log(config.username)   // correct
 console.log(config.admin.username) // user!
```
Конфиг со значениями по умолчанию

Если переменная окружения не определена — берём дефолтное значение:
```
import ‘dotenv/config’
 const config = {
  baseURL:  process.env.TEST_API_URL  ?? ‘…’,
  userId:   process.env.TEST_USER_ID ?? ‘…’,
  username: process.env.TEST_USERNAME ?? ‘…’,
  password: process.env.TEST_PASSWORD ?? ‘…’,
 }
 export default Object.freeze(config)
```
Оператор нулевого слияния (??)
```
const config = {
  isNull:     null    ?? ‘is null’,
  isUndefined:  undefined ?? ‘is undefined’,
  isNumber:    0     ?? ‘is number’,
  isEmptyString: ‘’    ?? ‘is empty string’
 }
 console.log(config)
```
Результат:
```
{
 isNull: ‘is null’,
 isUndefined: ‘is undefined’,
 isNumber: 0,
 isEmptyString: ‘’
}
```
Если нужно подменять и 0, и “” — использовать || вместо ??.

---
## Фикстуры 

Типы фикстур
*	Динамические - генерируются во время тестов, например, новые пользователи
*	Статические - подготовлены заранее, пример — ответ от API в JSON
--- 
### Генерация с faker:
```
import { faker } from ‘@faker-js/faker’

export function generateUserCredentials () {
  return {
   username: faker.internet.email(),
   password: ‘P@ssw0rd’
  }
 }
```
---

### Fetch API

Плюсы:
*	проще, чем XMLHttpRequest или node:http,
*	полный контроль.

Минусы:
*	больше кода, чем у axios/got/supertest,
*	нужно вручную парсить JSON.

Пример теста:
```
import { config } from ‘../framework’

describe(‘Авторизация’, () => {
  it(‘Успешная авторизация’, async () => {
   const url = ${config.baseURL}/Account/v1/GenerateToken

const response = await fetch(url, {
    method: ‘POST’,
    headers: { ‘Content-Type’: ‘application/json’ },
    body: JSON.stringify({
     userName: config.username,
     password: config.password
    }),
   })

expect(response.status).toBe(200)

const data = await response.json()
   expect(data.result).toBe(‘User authorized successfully.’)
   expect(data.token).toBeDefined()
  })
 })
```
---

### Supertest

Установка:
```bash
  $ npm install supertest –save-dev
```
Пример:
```
import { config } from ‘../framework’
 import supertest from ‘supertest’

describe(‘Авторизация’, () => {
  it(‘Успешная авторизация’, async () => {
   const response = await supertest(config.baseURL)
    .post(’/Account/v1/GenerateToken’)
    .send({
     userName: config.username,
     password: config.password,
    })

expect(response.status).toBe(200)
   expect(response.body.result).toBe(‘User authorized successfully.’)
   expect(response.body.token).toBeDefined()
  })
 })
```
---

### Got

ESM-библиотека. Нужно добавить:
```
transformIgnorePatterns: [’/node_modules/(?!got)/’]
```
Пример:
```
import { config } from ‘../framework’
 import got from ‘got’

describe(‘Авторизация’, () => {
  it(‘Успешная авторизация’, async () => {
   const url = ${config.baseURL}/Account/v1/GenerateToken

const response = await got.post(url, {
    json: {
     userName: config.username,
     password: config.password
    },
    responseType: ‘json’,
   })

expect(response.statusCode).toBe(200)
   expect(response.body.result).toBe(‘User authorized successfully.’)
   expect(response.body.token).toBeDefined()
  })
 })
```
---
### Axios и client.js

Создаём файл client.js в папке services:
```
import axios from ‘axios’
 import config from ‘../config/configDummy.json’

const client = axios.create({
  baseURL: config.baseURL,
  validateStatus: () => true
 })

export default client
```
---

Использование client.js в UserDummyService

Пример функции getUsers:
```
import client from ‘./client’

export const getUsers = async () => {
  const response = await client.get(’/users’)
  return {
   status: response.status,
   data: response.data
  }
 }
```
---

Логин через fetch

Создаём функцию login:
```
import config from ‘../config/configDummy.json’

export const login = async (username, password) => {
  const url = ${config.baseURL}/auth/login
  const response = await fetch(url, {
   method: ‘POST’,
   headers: { ‘Content-Type’: ‘application/json’ },
   body: JSON.stringify({
    username,
    password
   })
  })
  const data = await response.json()
  return {
   status: response.status,
   data
  }
 }
```
---

getMe с передачей токена (supertest)

Создаём метод getMe:
```
import supertest from ‘supertest’
 import config from ‘../config/configDummy.json’

export const getMe = async (token) => {
  const response = await supertest(config.baseURL)
   .get(’/auth/me’)
   .set(‘Authorization’, Bearer ${token})

return {
   status: response.status,
   data: response.body
  }
 }
```
---

Пример использования в тесте

Проверка получения токена и me:
```
import { login, getMe } from ‘../../services/UserDummyService’

describe(‘Проверка авторизации’, () => {
  it(‘Получение токена и данных о пользователе’, async () => {
   const loginResponse = await login(‘kminchelle’, ‘0lelplR’)
   expect(loginResponse.status).toBe(200)
   expect(loginResponse.data.token).toBeDefined()

const meResponse = await getMe(loginResponse.data.token)
   expect(meResponse.status).toBe(200)
   expect(meResponse.data.email).toBe(‘kminchelle@dummyjson.com’)
  })
 })
```
---

Экспорт и index.js

Добавляем экспорт всех методов в UserDummyService.js:
```
export {
  getUsers,
  login,
  getMe
 }
```
Заносим сервис в общий index.js:
```
export { default as config } from ‘./config/configDummy.json’
 export * as UserDummyService from ‘./services/UserDummyService’
```
---
Интеграция в тест auth.test.js

Переходим к файлу auth.test.js:
```
import { UserDummyService } from ‘../../framework’

describe(‘DummyJSON Auth’, () => {
  it(‘Авторизация и получение информации о себе’, async () => {
   const loginRes = await UserDummyService.login(‘kminchelle’, ‘0lelplR’)
   expect(loginRes.status).toBe(200)
   expect(loginRes.data.token).toBeDefined()

const meRes = await UserDummyService.getMe(loginRes.data.token)
   expect(meRes.status).toBe(200)
   expect(meRes.data.email).toBe(‘kminchelle@dummyjson.com’)
  })
 })
```
---

Возвращаем единый формат

В методах getUsers, login, getMe возвращаем объект в одном формате:
```
return {
  status: response.status,
  data: response.data || response.body
 }
```
Если используем fetch, остаётся response.json() — отдельно обрабатываем.

---

Запуск линтера
```bash
  npm run lint
```

Проверим, что структура, импорты и форматирование соответствуют стандарту проекта.

---

Структура экспорта сервисов

В UserDummyService.js:
```
export {
  getUsers,
  login,
  getMe
 }
```
В index.js фреймворка:
```
export * as UserDummyService from ‘./services/UserDummyService’
```
В корневом framework/index.js:
```
export { default as config } from ‘./config/configDummy.json’
 export * from ‘./services’
```
---

Унификация data/body

Чтобы не запутаться при смене библиотеки (axios/supertest/got), возвращаем данные в виде data, независимо от названия:
```
const parsedData = response.data || response.body
```
И возвращаем:
```
return {
  status: response.status || response.statusCode,
  data: parsedData
 }
```
Это защищает от ошибок типа:
```
expect(response.data).toEqual({ books })
 // вместо
 expect(response.body).toEqual({ books })
```
---

Работа с книгами (BooksService)

Пример вызова:
```
const response = await BookService.getBooks()
 expect(response.status).toBe(200)
 expect(response.data).toEqual({ books })
```
Если использовать разные библиотеки — применяем преобразование в data, чтобы expect был единым для всех.

---

Заключение
*	Все сервисы обёрнуты
*	Все ответы приведены к единому виду
*	Структура проекта: services, fixtures, config
*	Импорты централизованы через index.js
*	Поддерживается расширение (можно заменить библиотеку без переписывания логики теста)

___

Пример: получение токена и работа с книгами
```
describe(‘Работа с книгами’, () => {
  it(‘Удаление всех книг пользователя’, async () => {
   const loginRes = await UserDummyService.login(‘kminchelle’, ‘0lelplR’)
   const token = loginRes.data.token

const deleteRes = await UserBookService.deleteAllBooks(token)
   expect(deleteRes.status).toBe(204)
  })
 })
```
---

Разные библиотеки для разных сервисов

Допустим, BookService использует axios, а UserBookService — supertest, можно запутаться с тем, что использовать в ожидании:
```
expect(response.data).toEqual({ books })
 // или
 expect(response.body).toEqual({ books })
```
Чтобы избежать путаницы, делаем:
```
const parsedData = response.data || response.body
```
И возвращаем:
```
return {
  status: response.status || response.statusCode,
  data: parsedData
 }
```
---

Ошибки при несогласованности .data и .body

Если один сервис использует body, а другой data, то:
```
expect(response.data.books).toEqual([…])
 // может упасть, если data не определён
```
Решение — унификация через адаптацию на уровне сервиса.

---

Проверка авторизации без пароля (через AuthService)
```
it(‘Нельзя авторизоваться без пароля’, async () => {
  const response = await AuthService.generateToken({
   userName: config.username,
   password: ‘’,
  })
  expect(response.status).toBe(400)
  expect(response.data.code).toBe(‘1200’)
  expect(response.data.message).toBe(‘UserName and Password required.’)
 })
```
---

### Re-export модулей (централизованный импорт)

Раньше:
```
import config from ‘../framework/config/config’
 import AuthService from ‘../framework/services/AuthService’
```
Теперь можно так:
```
// в framework/index.js
 export { default as config } from ‘./config/config’
 export { default as AuthService } from ‘./services/AuthService’
```
Импорт будет короче:
```
import { config, AuthService } from ‘../framework’
```
Плюсы:
*	скрывает структуру проекта
*	упрощает импорты
*	легко масштабируется

---

Пример: index.js в services/

Создаём файл services/index.js:
```
export { default as AuthService } from ‘./AuthService’
 export { default as BookService } from ‘./BookService’
 export { default as UserBookService } from ‘./UserBookService’
 export * as UserDummyService from ‘./UserDummyService’
```
А в корневом framework/index.js:
```
export * from ‘./services’
 export { default as config } from ‘./config/configDummy.json’
```
Теперь можно импортировать из '../framework'.

---

VSCode: быстрый переход

Через:
```
import { AuthService } from ‘../../framework’
```

---

### Общая структура
```
project-root/
│
├── framework/
│   ├── config/ → конфиги
│   ├── fixtures/ → фикстуры
│   ├── services/ → API-обёртки
│   └── index.js → объединяющий экспорт
├── tests/ → тесты
└── package.json
```
---

Создание UserDummyService.js
1.	Создаём файл:
```
/services/UserDummyService.js
```
2.	Импортируем configDummy.json:
```
import config from ‘../config/configDummy.json’
```
3.	Добавляем fetch-запрос на логин:
```
export const login = async (username, password) => {
  const url = ${config.baseURL}/auth/login
  const response = await fetch(url, {
   method: ‘POST’,
   headers: { ‘Content-Type’: ‘application/json’ },
   body: JSON.stringify({ username, password })
  })
  const data = await response.json()
  return {
   status: response.status,
   data
  }
```
4.	Далее getMe с supertest:
```
import supertest from ‘supertest’

export const getMe = async (token) => {
  const response = await supertest(config.baseURL)
   .get(’/auth/me’)
   .set(‘Authorization’, Bearer ${token})
  return {
   status: response.status,
   data: response.body
  }
 }
```
---

Добавление axios-клиента (client.js)
1.	В /services/client.js:
```
import axios from ‘axios’
 import config from ‘../config/configDummy.json’

const client = axios.create({
  baseURL: config.baseURL,
  validateStatus: () => true
 })

export default client
```
2.	Использование:
```
import client from ‘./client’

export const getUsers = async () => {
  const response = await client.get(’/users’)
  return {
   status: response.status,
   data: response.data
  }
 }
```
---

Экспорт всех методов в UserDummyService.js
```
export {
  getUsers,
  login,
  getMe
 }
```
---

## Объединение в index.js

В /framework/index.js:
```
export { default as config } from ‘./config/configDummy.json’
 export * from ‘./services’
```
В /services/index.js:
```
export * as UserDummyService from ‘./UserDummyService’
```
---

Тест auth.test.js
```
import { UserDummyService } from ‘../../framework’

describe(‘DummyJSON Auth’, () => {
  it(‘Авторизация и получение данных о себе’, async () => {
   const loginRes = await UserDummyService.login(‘kminchelle’, ‘0lelplR’)
   expect(loginRes.status).toBe(200)
   expect(loginRes.data.token).toBeDefined()

const meRes = await UserDummyService.getMe(loginRes.data.token)
   expect(meRes.status).toBe(200)
   expect(meRes.data.email).toBe(‘kminchelle@dummyjson.com’)
  })
 })
```
---

Запуск линтера
```bash
  npm run lint
```
Проверяем, что:
*	структура корректна
*	сервисы обёрнуты
*	данные приходят в едином формате
*	авторизация и getMe работают на основе сервиса