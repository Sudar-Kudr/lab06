[![Build Status](https://travis-ci.com/Sudar-Kudr/lab05.svg?branch=main)](https://travis-ci.com/Sudar-Kudr/lab05)
## Laboratory work IV

Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса **Travis CI**

```sh
$ open https://travis-ci.org
```

## Tasks

- [ok] 1. Авторизоваться на сервисе **Travis CI** с использованием **GitHub** аккаунта
- [ok] 2. Создать публичный репозиторий с названием **lab05** на сервисе **GitHub**
- [ok] 3. Ознакомиться со ссылками учебного материала
- [ok] 4. Включить интеграцию сервиса **Travis CI** с созданным репозиторием
- [ok] 5. Получить токен для **Travis CLI** с правами **repo** и **user**
- [ok] 6. Получить фрагмент вставки значка сервиса **Travis CI** в формате **Markdown**
- [ok] 7. Выполнить инструкцию учебного материала
- [ok] 8. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Homework

Вы продолжаете проходить стажировку в "Formatter Inc." (см [подробности](https://github.com/tp-labs/lab03#Homework)).
В прошлый раз ваше задание заключалось в настройке автоматизированной системы **CMake**.
Сейчас вам требуется настроить систему непрерывной интеграции для библиотек и приложений, с которыми вы работали в [прошлый раз](https://github.com/tp-labs/lab03#Homework). Настройте сборочные процедуры на различных платформах:
* используйте [TravisCI](https://travis-ci.com/) для сборки на операционной системе **Linux** с использованием компиляторов **gcc** и **clang**;
* используйте [AppVeyor](https://www.appveyor.com/) для сборки на операционной системе **Windows**.

1. Создание файла .travis.yml, где определяем язык программирования, опер. систем. и компиляторы.
```sh
$ cat > .travis.yml <<EOF
language: cpp //язык программирования

compiler:     //компиляторы
- gcc
- clang
os:          //опер. систем
 - linux
  
script:
- cd solver_application
- cmake .
- make
- cd ..
- cd hello_world_application
- cmake .
- make

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
      - mingw-w64
EOF
```

2. В Travis CI появилась ошибка о пути, поэтому мне пришлось удалить несколько файлов в папке hello_world_application и в solver_application, потому что они както связаны с lab03 и это мешало тревису.

3. Запустил Travis CI и появилась ошибка в самом ```.cpp``` файле, поэтому добавил версию компилятора в solver_application/CMakeLists.txt эту строку:
```add_definitions(-std=c++11)```
которая говорит о том, что нужно использовать 11 версию С++.

4. Запустил Travis CI и все стало хорошо


```
Copyright (c) 2015-2021 The ISC Authors
```
