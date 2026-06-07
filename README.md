# Laboratory Work III
## Отчёт по третьей лабораторной работе: Изучение систем автоматизации сборки проекта на примере CMake

Данная лабораторная работа посвящена изучению систем автоматизации сборки проекта на примере **CMake**.

---

## Tutorial

### 1. Настройка переменных окружения

Задаём переменные для работы с аккаунтом GitHub:

```bash
$ export GITHUB_USERNAME=qwepyhbvc
$ export GITHUB_TOKEN=ghp_************************************
$ alias edit=nano
```

### 2. Подготовка репозитория

Клонируем репозиторий `Lab_02` (обратите внимание на заглавную букву) как основу для `lab03`:

```bash
$ cd ~/wowtt/workspace/projects
$ git clone https://github.com/qwepyhbvc/Lab_02 lab03
Cloning into 'lab03'...
remote: Enumerating objects: 21, done.
remote: Counting objects: 100% (21/21), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 21 (delta 6), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (21/21), 17.73 KiB | 2.95 MiB/s, done.
Resolving deltas: 100% (6/6), done.
```

Проверяем содержимое клонированного репозитория:

```bash
$ cd lab03
$ ls -la
total 36
drwxr-xr-x 3 wowtt wowtt  4096 Jun  7 13:14 .
drwxr-xr-x 7 wowtt wowtt  4096 Jun  7 13:14 ..
drwxr-xr-x 8 wowtt wowtt  4096 Jun  7 13:14 .git
-rw-r--r-- 1 wowtt wowtt  1066 Jun  7 13:14 LICENSE
-rw-r--r-- 1 wowtt wowtt 18731 Jun  7 13:14 README.md
```

Удаляем старый remote и добавляем новый для `lab03`:

```bash
$ git remote -v
origin  https://github.com/qwepyhbvc/Lab_02 (fetch)
origin  https://github.com/qwepyhbvc/Lab_02 (push)

$ git remote remove origin
$ git remote add origin https://qwepyhbvc:${GITHUB_TOKEN}@github.com/qwepyhbvc/lab03.git

$ git remote -v
origin  https://qwepyhbvc:ghp_************************************@github.com/qwepyhbvc/lab03.git (fetch)
origin  https://qwepyhbvc:ghp_************************************@github.com/qwepyhbvc/lab03.git (push)

$ git branch
* main

$ git push -u origin main
Enumerating objects: 21, done.
Counting objects: 100% (21/21), done.
Delta compression using up to 12 threads
Compressing objects: 100% (13/13), done.
Writing objects: 100% (21/21), 17.73 KiB | 17.73 MiB/s, done.
Total 21 (delta 6), reused 21 (delta 6), pack-reused 0
remote: Resolving deltas: 100% (6/6), done.
To https://github.com/qwepyhbvc/lab03.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

### 3. Создание структуры проекта

В репозитории были только файлы `LICENSE` и `README.md`, поэтому создаём структуру проекта:

```bash
$ mkdir -p sources include examples
```

**include/print.hpp:**

```bash
$ cat > include/print.hpp << 'EOF'
#include <fstream>
#include <iostream>
#include <string>

void print(const std::string& text, std::ofstream& out);
void print(const std::string& text, std::ostream& out = std::cout);
EOF
```

**sources/print.cpp:**

```bash
$ cat > sources/print.cpp << 'EOF'
#include <print.hpp>

void print(const std::string& text, std::ostream& out)
{
  out << text;
}

void print(const std::string& text, std::ofstream& out)
{
  out << text;
}
EOF
```

**examples/example1.cpp:**

```bash
$ cat > examples/example1.cpp << 'EOF'
#include <print.hpp>

int main(int argc, char** argv)
{
  print("hello");
}
EOF
```

**examples/example2.cpp:**

```bash
$ cat > examples/example2.cpp << 'EOF'
#include <print.hpp>

#include <fstream>

int main(int argc, char** argv)
{
  std::ofstream file("log.txt");
  print(std::string("hello"), file);
}
EOF
```

Проверяем созданную структуру:

```bash
$ ls -la
total 48
drwxr-xr-x 6 wowtt wowtt  4096 Jun  7 13:21 .
drwxr-xr-x 7 wowtt wowtt  4096 Jun  7 13:14 ..
drwxr-xr-x 8 wowtt wowtt  4096 Jun  7 13:18 .git
-rw-r--r-- 1 wowtt wowtt  1066 Jun  7 13:14 LICENSE
-rw-r--r-- 1 wowtt wowtt 18731 Jun  7 13:14 README.md
drwxr-xr-x 2 wowtt wowtt  4096 Jun  7 13:22 examples
drwxr-xr-x 2 wowtt wowtt  4096 Jun  7 13:21 include
drwxr-xr-x 2 wowtt wowtt  4096 Jun  7 13:22 sources

$ ls -la sources/
total 12
drwxr-xr-x 2 wowtt wowtt 4096 Jun  7 13:22 .
drwxr-xr-x 6 wowtt wowtt 4096 Jun  7 13:21 ..
-rw-r--r-- 1 wowtt wowtt  172 Jun  7 13:22 print.cpp

$ ls -la include/
total 12
drwxr-xr-x 2 wowtt wowtt 4096 Jun  7 13:21 .
drwxr-xr-x 6 wowtt wowtt 4096 Jun  7 13:21 ..
-rw-r--r-- 1 wowtt wowtt  183 Jun  7 13:21 print.hpp

$ ls -la examples/
total 16
drwxr-xr-x 2 wowtt wowtt 4096 Jun  7 13:22 .
drwxr-xr-x 6 wowtt wowtt 4096 Jun  7 13:21 ..
-rw-r--r-- 1 wowtt wowtt   76 Jun  7 13:22 example1.cpp
-rw-r--r-- 1 wowtt wowtt  148 Jun  7 13:22 example2.cpp
```

Сохраняем изменения в Git:

```bash
$ git add sources/ include/ examples/
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   examples/example1.cpp
        new file:   examples/example2.cpp
        new file:   include/print.hpp
        new file:   sources/print.cpp

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        print.o

$ git commit -m "Add source code: print library and examples"
[main 5755b69] Add source code: print library and examples
 4 files changed, 32 insertions(+)
 create mode 100644 examples/example1.cpp
 create mode 100644 examples/example2.cpp
 create mode 100644 include/print.hpp
 create mode 100644 sources/print.cpp

$ git push origin main
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 12 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (9/9), 953 bytes | 953.00 KiB/s, done.
Total 9 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/qwepyhbvc/lab03.git
   7f3f5e3..5755b69  main -> main
```

### 4. Ручная компиляция (без CMake)

Компиляция объектного файла и создание статической библиотеки:

```bash
$ g++ -std=c++11 -I./include -c sources/print.cpp
$ ls print.o
print.o

$ ar rvs print.a print.o
ar: creating print.a
a - print.o

$ file print.a
print.a: current ar archive
```

Компиляция и запуск `example1`:

```bash
$ g++ -std=c++11 -I./include -c examples/example1.cpp
$ ls example1.o
example1.o

$ g++ example1.o print.a -o example1
$ ./example1 && echo
hello
```

Компиляция и запуск `example2`:

```bash
$ g++ -std=c++11 -I./include -c examples/example2.cpp
$ g++ example2.o print.a -o example2
$ ./example2
$ cat log.txt && echo
hello
```

Очистка временных файлов:

```bash
$ rm -rf example1.o example2.o print.o print.a example1 example2 log.txt
```

### 5. Сборка с CMake

**Создание базового CMakeLists.txt:**

```bash
$ cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.4)
project(print)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.4)
project(print)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
```

**Конфигурация и сборка:**

```bash
$ cmake -H. -B_build
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.
  Update the VERSION argument <min> value or use a ...<max> suffix.
-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info - done
-- Detecting CXX compiler ABI info - done
-- Configuring done (3.5s)
-- Generating done (0.0s)
-- Build files have been written to: /home/wowtt/wowtt/workspace/projects/lab03/_build

$ cmake --build _build
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print

$ ls -la _build/libprint.a
-rw-r--r-- 1 wowtt wowtt 2246 Jun  7 13:27 _build/libprint.a
```

**Добавление исполняемых файлов:**

```bash
$ cat >> CMakeLists.txt << 'EOF'

add_executable(example1 ${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 ${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.4)
project(print)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

add_executable(example1 ${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 ${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)

target_link_libraries(example1 print)
target_link_libraries(example2 print)

$ cmake --build _build
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed...
-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/wowtt/wowtt/workspace/projects/lab03/_build
[ 33%] Built target print
[ 50%] Building CXX object CMakeFiles/example1.dir/examples/example1.cpp.o
[ 66%] Linking CXX executable example1
[ 66%] Built target example1
[ 83%] Building CXX object CMakeFiles/example2.dir/examples/example2.cpp.o
[100%] Linking CXX executable example2
[100%] Built target example2

$ ./_build/example1 && echo
hello

$ ./_build/example2
$ cat log.txt && echo
hello
```

**Добавление установки (install):**

```bash
$ git clone https://github.com/tp-labs/lab03 tmp
Cloning into 'tmp'...
remote: Enumerating objects: 91, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 91 (delta 23), reused 21 (delta 21), pack-reused 61 (from 1)
Receiving objects: 100% (91/91), 1.02 MiB | 3.02 MiB/s, done.
Resolving deltas: 100% (41/41), done.

$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)

$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed...
-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/wowtt/wowtt/workspace/projects/lab03/_build

$ cmake --build _build --target install
[100%] Built target print
Install the project...
-- Install configuration: ""
-- Installing: /home/wowtt/wowtt/workspace/projects/lab03/_install/lib/libprint.a
-- Installing: /home/wowtt/wowtt/workspace/projects/lab03/_install/include
-- Installing: /home/wowtt/wowtt/workspace/projects/lab03/_install/include/print.hpp
-- Installing: /home/wowtt/wowtt/workspace/projects/lab03/_install/cmake/print-config.cmake
-- Installing: /home/wowtt/wowtt/workspace/projects/lab03/_install/cmake/print-config-noconfig.cmake

$ tree _install
_install
├── cmake
│   ├── print-config-noconfig.cmake
│   └── print-config.cmake
├── include
│   └── print.hpp
└── lib
    └── libprint.a

4 directories, 4 files
```

### 6. Сохранение изменений в Git

```bash
$ git add CMakeLists.txt
$ git commit -m "added CMakeLists.txt with install target"
[main 2127163] added CMakeLists.txt with install target
 1 file changed, 36 insertions(+)
 create mode 100644 CMakeLists.txt

$ git push origin main
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 12 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 771 bytes | 771.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/qwepyhbvc/lab03.git
   5755b69..2127163  main -> main
```

---

## Homework: Formatter Inc.

### Задание 1: formatter_lib/CMakeLists.txt

```bash
$ cd ~/wowtt/workspace/projects
$ mkdir formatter_project
$ cd formatter_project

$ git clone https://github.com/tp-labs/lab03.git tmp
Cloning into 'tmp'...
remote: Enumerating objects: 91, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 91 (delta 23), reused 21 (delta 21), pack-reused 61 (from 1)
Receiving objects: 100% (91/91), 1.02 MiB | 3.35 MiB/s, done.
Resolving deltas: 100% (41/41), done.

$ mv tmp/formatter_lib .
$ mv tmp/formatter_ex_lib .
$ mv tmp/hello_world_application .
$ mv tmp/solver_lib .
$ mv tmp/solver_application .
$ rm -rf tmp

$ cd formatter_lib
$ cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.10)
project(formatter_lib VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp)

target_include_directories(formatter PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

install(TARGETS formatter
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/formatter.h
        DESTINATION include)
EOF

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(formatter_lib VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp)

target_include_directories(formatter PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

install(TARGETS formatter
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/formatter.h
        DESTINATION include)
```

### Задание 2: formatter_ex_lib/CMakeLists.txt

```bash
$ cd ../formatter_ex_lib
$ cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.10)
project(formatter_ex_lib VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter_ex STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.cpp)

target_include_directories(formatter_ex PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(formatter_ex PRIVATE formatter)

install(TARGETS formatter_ex
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.h
        DESTINATION include)
EOF

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(formatter_ex_lib VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter_ex STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.cpp)

target_include_directories(formatter_ex PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(formatter_ex PRIVATE formatter)

install(TARGETS formatter_ex
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.h
        DESTINATION include)
```

### Задание 3: Приложения

**hello_world_application/CMakeLists.txt:**

```bash
$ cd ../hello_world_application
$ cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.10)
project(hello_world_app VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world ${CMAKE_CURRENT_SOURCE_DIR}/hello_world.cpp)
target_link_libraries(hello_world PRIVATE formatter_ex)

install(TARGETS hello_world
        RUNTIME DESTINATION bin)
EOF

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(hello_world_app VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world ${CMAKE_CURRENT_SOURCE_DIR}/hello_world.cpp)
target_link_libraries(hello_world PRIVATE formatter_ex)

install(TARGETS hello_world
        RUNTIME DESTINATION bin)
```

**solver_lib/CMakeLists.txt:**

```bash
$ cd ../solver_lib
$ cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.10)
project(solver_lib VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(solver STATIC ${CMAKE_CURRENT_SOURCE_DIR}/solver.cpp)

target_include_directories(solver PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

install(TARGETS solver
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/solver.h
        DESTINATION include)
EOF

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(solver_lib VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(solver STATIC ${CMAKE_CURRENT_SOURCE_DIR}/solver.cpp)

target_include_directories(solver PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

install(TARGETS solver
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/solver.h
        DESTINATION include)
```

**solver_application/CMakeLists.txt:**

```bash
$ cd ../solver_application
$ cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.10)
project(solver_app VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(equation ${CMAKE_CURRENT_SOURCE_DIR}/equation.cpp)
target_link_libraries(equation PRIVATE formatter_ex solver)

install(TARGETS equation
        RUNTIME DESTINATION bin)
EOF

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(solver_app VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(equation ${CMAKE_CURRENT_SOURCE_DIR}/equation.cpp)
target_link_libraries(equation PRIVATE formatter_ex solver)

install(TARGETS equation
        RUNTIME DESTINATION bin)
```

### Исправление кода программ

**solver.cpp (добавлен заголовочный файл `<cmath>` и исправлен вызов `sqrt`):**

```bash
$ cat > solver.cpp << 'EOF'
#include "solver.h"
#include <cmath>

void solve(float a, float b, float c, float& x1, float& x2)
{
    float d = b*b - 4*a*c;
    if (d < 0) {
        x1 = x2 = 0;
        return;
    }
    x1 = (-b - std::sqrt(static_cast<double>(d))) / (2 * a);
    x2 = (-b + std::sqrt(static_cast<double>(d))) / (2 * a);
}
EOF

$ cat solver.cpp
#include "solver.h"
#include <cmath>

void solve(float a, float b, float c, float& x1, float& x2)
{
    float d = b*b - 4*a*c;
    if (d < 0) {
        x1 = x2 = 0;
        return;
    }
    x1 = (-b - std::sqrt(static_cast<double>(d))) / (2 * a);
    x2 = (-b + std::sqrt(static_cast<double>(d))) / (2 * a);
}
```

**hello_world.cpp:**

```bash
$ cd ../hello_world_application
$ cat > hello_world.cpp << 'EOF'
#include <iostream>
#include <string>
#include "formatter_ex.h"

int main()
{
    std::string message = "hello, world!\n";
    formatter(std::cout, message);
    return 0;
}
EOF

$ cat hello_world.cpp
#include <iostream>
#include <string>
#include "formatter_ex.h"

int main()
{
    std::string message = "hello, world!\n";
    formatter(std::cout, message);
    return 0;
}
```

**equation.cpp:**

```bash
$ cd ../solver_application
$ cat > equation.cpp << 'EOF'
#include <iostream>
#include <string>
#include "formatter_ex.h"
#include "solver.h"

int main()
{
    float a, b, c, x1, x2;
    
    std::cout << "Enter coefficients a, b, c: ";
    std::cin >> a >> b >> c;
    
    solve(a, b, c, x1, x2);
    
    std::string output = "Equation: " + std::to_string(a) + "x^2 + " + std::to_string(b) + "x + " + std::to_string(c) + " = 0\n";
    output += "Roots: x1 = " + std::to_string(x1) + ", x2 = " + std::to_string(x2) + "\n";
    
    formatter(std::cout, output);
    
    return 0;
}
EOF

$ cat equation.cpp
#include <iostream>
#include <string>
#include "formatter_ex.h"
#include "solver.h"

int main()
{
    float a, b, c, x1, x2;
    
    std::cout << "Enter coefficients a, b, c: ";
    std::cin >> a >> b >> c;
    
    solve(a, b, c, x1, x2);
    
    std::string output = "Equation: " + std::to_string(a) + "x^2 + " + std::to_string(b) + "x + " + std::to_string(c) + " = 0\n";
    output += "Roots: x1 = " + std::to_string(x1) + ", x2 = " + std::to_string(x2) + "\n";
    
    formatter(std::cout, output);
    
    return 0;
}
```

### Корневой CMakeLists.txt

```bash
$ cd ~/wowtt/workspace/projects/formatter_project
$ cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.10)
project(formatter_project VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(solver_lib)
add_subdirectory(hello_world_application)
add_subdirectory(solver_application)
EOF

$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(formatter_project VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(solver_lib)
add_subdirectory(hello_world_application)
add_subdirectory(solver_application)
```

### Сборка всего проекта

```bash
$ rm -rf build
$ mkdir build
$ cd build

$ cmake .. -DCMAKE_INSTALL_PREFIX=./install
-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info - done
-- Detecting CXX compiler ABI info - done
-- Configuring done (3.2s)
-- Generating done (0.0s)
-- Build files have been written to: /home/wowtt/wowtt/workspace/projects/formatter_project/build

$ cmake --build .
[ 10%] Building CXX object formatter_lib/CMakeFiles/formatter.dir/formatter.cpp.o
[ 20%] Linking CXX static library libformatter.a
[ 20%] Built target formatter
[ 30%] Building CXX object formatter_ex_lib/CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[ 40%] Linking CXX static library libformatter_ex.a
[ 40%] Built target formatter_ex
[ 50%] Building CXX object solver_lib/CMakeFiles/solver.dir/solver.cpp.o
[ 60%] Linking CXX static library libsolver.a
[ 60%] Built target solver
[ 70%] Building CXX object hello_world_application/CMakeFiles/hello_world.dir/hello_world.cpp.o
[ 80%] Linking CXX executable hello_world
[ 80%] Built target hello_world
[ 90%] Building CXX object solver_application/CMakeFiles/equation.dir/equation.cpp.o
[100%] Linking CXX executable equation
[100%] Built target equation

$ cmake --install .
-- Install configuration: ""
-- Installing: .../install/lib/libformatter.a
-- Installing: .../install/include/formatter.h
-- Installing: .../install/lib/libformatter_ex.a
-- Installing: .../install/include/formatter_ex.h
-- Installing: .../install/lib/libsolver.a
-- Installing: .../install/include/solver.h
-- Installing: .../install/bin/hello_world
-- Installing: .../install/bin/equation

$ tree install
install
├── bin
│   ├── equation
│   └── hello_world
├── include
│   ├── formatter.h
│   ├── formatter_ex.h
│   └── solver.h
└── lib
    ├── libformatter.a
    ├── libformatter_ex.a
    └── libsolver.a

4 directories, 8 files
```

### Проверка работы приложений

```bash
$ ./install/bin/hello_world
hello, world!

$ ./install/bin/equation
Enter coefficients a, b, c: 1 -3 2
Equation: 1.000000x^2 + -3.000000x + 2.000000 = 0
Roots: x1 = 1.000000, x2 = 2.000000

$ ./install/bin/equation
Enter coefficients a, b, c: 1 2 10
Equation: 1.000000x^2 + 2.000000x + 10.000000 = 0
Roots: x1 = 0.000000, x2 = 0.000000
```

---

## Выводы

В ходе выполнения лабораторной работы были изучены:

1. **Ручная компиляция C++ проектов:**
   - Компиляция объектных файлов: `g++ -c`
   - Создание статических библиотек: `ar rvs`
   - Линковка библиотек с исполняемыми файлами

2. **Система автоматизации сборки CMake:**
   - Написание `CMakeLists.txt` для библиотек и приложений
   - Конфигурация проекта: `cmake -H. -B_build`
   - Сборка проекта: `cmake --build _build`
   - Установка проекта: `cmake --build _build --target install`

3. **Многоуровневая структура проекта:**
   - Создание библиотек с зависимостями (`formatter_ex` зависит от `formatter`)
   - Подключение поддиректорий через `add_subdirectory()`
   - Линковка библиотек через `target_link_libraries()`

4. **Управление зависимостями:**
   - Использование `PRIVATE` для внутренних зависимостей
   - Публичные заголовочные файлы через `target_include_directories`

5. **Установка проекта:**
   - Указание префикса установки: `-DCMAKE_INSTALL_PREFIX=./install`
   - Копирование библиотек, заголовков и исполняемых файлов

### Основные команды CMake

| Команда | Назначение |
|---------|------------|
| `cmake_minimum_required()` | Установка минимальной версии CMake |
| `project()` | Определение имени проекта |
| `set(CMAKE_CXX_STANDARD 11)` | Установка стандарта C++ |
| `add_library()` | Создание статической/динамической библиотеки |
| `add_executable()` | Создание исполняемого файла |
| `target_include_directories()` | Указание путей к заголовочным файлам |
| `target_link_libraries()` | Линковка библиотек |
| `add_subdirectory()` | Добавление подпроекта |
| `install()` | Настройка установки файлов |
