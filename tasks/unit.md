# Пробуем unit тесты своими руками

## Цель
В этой ДЗ знакомимся с синтаксисом обычного теста и параметризированного теста в jest. И пробуем применить принцип AAA на практике.

## Задание
### Первая часть

*Способ на ваш выбор (рекоммендую второй)*

**Способ первый:**
1. Настроить окружение (как минимум jest)
2. Скопировать файл https://github.com/OTUS-QA-JS/otus-qajs/blob/lesson/unit-test/src/app.js

**Способ второй:**
1. git clone https://github.com/OTUS-QA-JS/otus-qajs.git
2. git switch lesson/unit-test
3. git remote remove origin
4. git remote add origin -- git@github.com:ВАШ_НИК/НАЗВАНИЕ_РЕПОЗИТОРИЯ.git
5. git checkout -b task/unit-test
6. npm install



### Вторая часть

В папке `test` создайте файл  `app.test.js`
В этом файле напиши тесты на все функции из файла `src/app.js`
Как минимум 3 теста на каждую функцию. Тест одной из функций должен быть параметризированным.


Для написания параматеризинного теста можете использовать [test.each(table)(name, fn, timeout)](https://jestjs.io/docs/api#testeachtablename-fn-timeout)


Для проверки (ревью) домашнего задания присылайте в чат ссылку с pull request в вашем репозитории


---

## Критерии оценивания
1. Все функции покрыты тестами.
2. Есть параметризованные тесты.