## Laboratory work VI

Данная лабораторная работа посвещена изучению средств пакетирования на примере **CPack**

```sh
$ open https://cmake.org/Wiki/CMake:CPackPackageGenerators
```

## Tasks

- [ok] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [ok] 2. Выполнить инструкцию учебного материала
- [ok] 3. Ознакомиться со ссылками учебного материала
- [ok] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=<имя_пользователя>        #присваиваем имя пользователя GitHub в переменную GITHUB_USERNAME
$ export GITHUB_EMAIL=<адрес_почтового_ящика>     #присваиваем адрес почты в переменную GITHUB_EMAIL
$ alias edit=<nano|vi|vim|subl>                 #выбираем какой редактор хотим открыть
$ alias gsed=sed # for *-nix system            #заменяем команду sed на gsed
```

```sh
$ cd ${GITHUB_USERNAME}/workspace     #спускаемся в workspace
$ pushd .                            #добавляем в стек текущий каталог
$ source scripts/activate           #выполняем скрипт
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06   #клонируем репозиторий из lab05 в директорию projects/lab06
$ cd projects/lab06                                                     #переходим директорию projects/lab06
$ git remote remove origin                                             #удаляем старую ссылку репозитория
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06   #добавляем ссылку репозитория в управление репозиториями
```

```sh
#добавляем в CMakeLists.txt данные, отвечающие за сборку тестов ("-е" – особенность MacOs)
$ gsed -i -e '/project(print)/a\
set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")
' CMakeLists.txt
$ gsed -i -e '/project(print)/a\
set(PRINT_VERSION\
  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})
' CMakeLists.txt
$ gsed -i -e '/project(print)/a\
set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
$ gsed -i -e '/project(print)/a\
set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
$ gsed -i -e '/project(print)/a\
set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
$ gsed -i -e '/project(print)/a\
set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt

#cмотрим изменения в файле CMakeLists.txt
$ git diff
diff --git a/CMakeLists.txt b/CMakeLists.txt
index 52e94e4..434cd4e 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -6,6 +6,13 @@ set(CMAKE_CXX_STANDARD_REQUIRED ON)
 option(BUILD_EXAMPLES "Build examples" OFF)
 
 project(print)
+set(PRINT_VERSION_MAJOR 0)
+set(PRINT_VERSION_MINOR 1)
+set(PRINT_VERSION_PATCH 0)
+set(PRINT_VERSION_TWEAK 0)
+set(PRINT_VERSION
+${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
+set(PRINT_VERSION_STRING "v${PRINT_VERSION}")
 
 add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
```

```sh
$ touch DESCRIPTION && edit DESCRIPTION              #cоздаем файл DESCRIPTION и редактируем
$ touch ChangeLog.md                                  #cоздаем файла ChangeLog.md
$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"       #cоздаем переменную DATE
$ cat > ChangeLog.md <<EOF                              #записываем в файл ChangeLog.md переменные DATE, GITHUB_USERNAME и GITHUB_EMAIL
* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```

```sh
$ cat > CPackConfig.cmake <<EOF             #создаем и подключаем библиотеку
include(InstallRequiredSystemLibraries)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF             #ставим значения переменных в пакете
set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF             #добавляем пакет в файл

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF             #настраиваем CPACK_RPM_PACKAGE

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF             #настраиваем CPACK_DEBIAN_PACKAGE

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF             #подключаем модуль CPack

include(CPack)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF                #подключаем CPackConfig.cmake

include(CPackConfig.cmake)
EOF
```

```sh
$ gsed -i -e 's/lab05/lab06/g' README.md     #меняем название с lab05 на lab06 в файле README.md
```

```sh
$ git add .                            #добавляем все файлы, в которых были изменения
$ git commit -m "added cpack config"    #закомментируем все файлы, в которых были изменения
...11 files changed, 160 insertions(+), 2 deletions(-)...
$ git tag v0.1.0.0                       #cоздаем тэг с версией проекта
$ git push origin main --tags             #отправляем данные на сервер, в удаленный репозиторий main вместе с тэгом
...To https://github.com/Sudar-Kudr/lab06
* [new branch]      main -> main
* [new tag]         v0.1.0.0 -> v0.1.0.0
```

```sh
$ travis login --auto         #авторизируемся на travis
$ travis enable             #делаем проект доступным
```

```sh
$ cmake -H. -B_build         #устанавливаем в директорию _build файлы сборки
$ cmake --build _build        #запускаем сборку
$ cd _build                    #спускаемся в _build
$ cpack -G "TGZ"              #архивируем пакет
$ cd ..                      #поднимаемся обратно
```

```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"       #генерируем и создаем файлы для сборки с помощью генератора сборки "TGZ"
$ cmake --build _build --target package           #запускаем сборку package
```

```sh
$ mkdir artifacts                 #создаем директорию artifacts
$ mv _build/*.tar.gz artifacts   #перемещаем файлы с разрешением .tar.gz в папку artifacts
$ tree artifacts                #выводим в терминал структуру папки artifacts, в виде "дерева"
```

## Report

```sh
$ popd                                                                           #удаляем из стека текущий каталог
$ export LAB_NUMBER=06                                                          #присваиваем 06 в переменную LAB_NUMBER
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER} #клонируем из ссылки в директорию (в нашем случае-tasks/lab06)
$ mkdir reports/lab${LAB_NUMBER}                                              #создаем в директории reports папку (в нашем случае- lab06) 
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md     #копируем из одной директории в другую
$ cd reports/lab${LAB_NUMBER}                                               #спускаемся в директорию (в наше случае- lab06)
$ edit REPORT.md                                                           #редактируем REPORT.md
$ gist REPORT.md                                                          #сохраняем REPORT.md
```

## Homework

После того, как вы настроили взаимодействие с системой непрерывной интеграции,</br>
обеспечив автоматическую сборку и тестирование ваших изменений, стоит задуматься</br>
о создание пакетов для измениний, которые помечаются тэгами (см. вкладку [releases](https://github.com/tp-labs/lab06/releases)).</br>
Пакет должен содержать приложение _solver_ из [предыдущего задания](https://github.com/tp-labs/lab03#задание-1)
Таким образом, каждый новый релиз будет состоять из следующих компонентов:
- архивы с файлами исходного кода (`.tar.gz`, `.zip`)
- пакеты с бинарным файлом _solver_ (`.deb`, `.rpm`, `.msi`, `.dmg`)

В качестве подсказки:
```sh
$ cat .travis.yml
os: osx
script:
...
- cpack -G DragNDrop # dmg

$ cat .travis.yml
os: linux
script:
...
- cpack -G DEB # deb

$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
...
- cpack -G RPM # rpm

$ cat appveyor.yml
platform:
- x86
- x64
build_script:
...
- cpack -G WIX # msi
```

Для этого нужно добавить ветвление в конфигурационные файлы для **CI** со следующей логикой:</br>
если **commit** помечен тэгом, то необходимо собрать пакеты (`DEB, RPM, WIX, DragNDrop, ...`) </br>
и разместить их на сервисе **GitHub**. (см. пример для [Travi CI](https://docs.travis-ci.com/user/deployment/releases))</br>

## Links

- [DMG](https://cmake.org/cmake/help/latest/module/CPackDMG.html)
- [DEB](https://cmake.org/cmake/help/latest/module/CPackDeb.html)
- [RPM](https://cmake.org/cmake/help/latest/module/CPackRPM.html)
- [NSIS](https://cmake.org/cmake/help/latest/module/CPackNSIS.html)

```
Copyright (c) 2015-2021 The ISC Authors
```
