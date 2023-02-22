---
layout: post
title:  "《Modern CMake》读书笔记"
date:   2023-02-22 17:00:00 +0800
categories: book cmake
---

## Running CMake

### Building a project

```bash
mkdir build
cd build
cmake ..
make # cmake --build .
```

or 

```bash
cmake -S . -B build
cmake --build build
```

install

```bash
# from build directory
make install
cmake --build . --target install
cmake --install .
# from source directory
make -C build install
cmake --build build --target install
cmake --install build
```

### Picking a compiler

```bash
CC=clang Cxx=clang++ cmake ..
```

### Picking a generator

`cmake --help`

```
Generators

The following generators are available on this platform:
  Unix Makefiles               = Generates standard UNIX makefiles.
  Ninja                        = Generates build.ninja files.
  Watcom WMake                 = Generates Watcom WMake makefiles.
  CodeBlocks - Ninja           = Generates CodeBlocks project files.
  CodeBlocks - Unix Makefiles  = Generates CodeBlocks project files.
  CodeLite - Ninja             = Generates CodeLite project files.
  CodeLite - Unix Makefiles    = Generates CodeLite project files.
  Eclipse CDT4 - Ninja         = Generates Eclipse CDT 4.0 project files.
  Eclipse CDT4 - Unix Makefiles= Generates Eclipse CDT 4.0 project files.
  KDevelop3                    = Generates KDevelop 3 project files.
  KDevelop3 - Unix Makefiles   = Generates KDevelop 3 project files.
  Kate - Ninja                 = Generates Kate project files.
  Kate - Unix Makefiles        = Generates Kate project files.
  Sublime Text 2 - Ninja       = Generates Sublime Text 2 project files.
  Sublime Text 2 - Unix Makefiles
                               = Generates Sublime Text 2 project files.
```

```bash
cmake -G "Unix Makefiles"
```

### Setting options

Verbose and partial builds

```bash
cmake --build build --verbose
VERBOSE=1 make
```

## CMake 行为准则(Do’s and Don’ts)

### CMake 应避免的行为

接下来的两个列表很大程度上基于优秀的 gist Effective Modern CMake. 那个列表更长且更详细，也非常欢迎你去仔细阅读它。

- 不要使用具有全局作用域的函数：这包含 link_directories、 include_libraries 等相似的函数。
- 不要添加非必要的 PUBLIC 要求：你应该避免把一些不必要的东西强加给用户（-Wall）。相比于 PUBLIC，更应该把他们声明为 PRIVATE。
- 不要在file函数中添加 GLOB 文件：如果不重新运行 CMake，Make 或者其他的工具将不会知道你是否添加了某个文件。值得注意的是，CMake 3.12 添加了一个 CONFIGURE_DEPENDS 标志能够使你更好的完成这件事。
- 将库直接链接到需要构建的目标上：如果可以的话，总是显式的将库链接到目标上。
- 当链接库文件时，不要省略 PUBLIC或PRIVATE 关键字：这将会导致后续所有的链接都是缺省的。

### CMake 应遵守的规范

- 把 CMake 程序视作代码：它是代码。它应该和其他的代码一样，是整洁并且可读的。
- 建立目标的观念：你的目标应该代表一系列的概念。为任何需要保持一致的东西指定一个 （导入型）INTERFACE 目标，然后每次都链接到该目标。
- 导出你的接口：你的 CMake 项目应该可以直接构建或者安装。
- 为库书写一个 Config.cmake 文件：这是库作者为支持客户的体验而应该做的。
- 声明一个 ALIAS 目标以保持使用的一致性：使用 add_subdirectory 和 find_package 应该提供相同的目标和命名空间。
- 将常见的功能合并到有详细文档的函数或宏中：函数往往是更好的选择。
- 使用小写的函数名： CMake 的函数和宏的名字可以定义为大写或小写，但是一般都使用小写，变量名用大写。
- 使用 cmake_policy 和/或 限定版本号范围： 每次改变版本特性 (policy) 都要有据可依。应该只有不得不使用旧特性时才降低特性 (policy) 版本。

## Introduction to the basics

### Minimum Version

`CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.25)
```

### Setting a project

```cmake
project(MyProject VERSION 1.0
	DESCRIPTION "Very nice project"
	LANGUAGES CXX)
```

### Make an executable

```cmake
add_executable(one two.cpp three.h)
```

### Making a library

```cmake
add_library(one STATIC two.cpp three.h)
```

### Targets are your friend

```cmake
target_include_directories(one PUBLIC include)
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
```

### Dive in

```cmake
cmake_minimum_required(VERSION 3.8)

project(Calculator LANGUAGES CXX)

add_library(calclib STATIC src/calclib.cpp include/calc/lib.hpp)
target_include_directories(calclib PUBLIC include)
target_compile_features(calclib PUBLIC cxx_std_11)

add_executable(calc apps/calc.cpp)
target_link_libraries(calc PUBLIC calclib)
```

## 如何组织你的项目

```bash
- project
  - .gitignore
  - README.md
  - LICENCE.md
  - CMakeLists.txt
  - cmake
    - FindSomeLib.cmake
    - something_else.cmake
  - include
    - project
      - lib.hpp
  - src
    - CMakeLists.txt
    - lib.cpp
  - apps
    - CMakeLists.txt
    - app.cpp
  - tests
    - CMakeLists.txt
    - testlib.cpp
  - docs
    - CMakeLists.txt
  - extern
    - googletest
  - scripts
    - helper.py
```

### Example

```cmake
# Almost all CMake files should start with this
# You should always specify a range with the newest
# and oldest tested versions of CMake. This will ensure
# you pick up the best policies.
cmake_minimum_required(VERSION 3.1...3.21)
# This is your project statement. You should always list languages;
# Listing the version is nice here since it sets lots of useful variables
project(
  ModernCMakeExample
  VERSION 1.0
  LANGUAGES CXX)
# If you set any CMAKE_ variables, that can go here.
# (But usually don't do this, except maybe for C++ standard)
# Find packages go here.
# You should usually split this into folders, but this is a simple example
# This is a "default" library, and will match the *** variable setting.
# Other common choices are STATIC, SHARED, and MODULE
# Including header files here helps IDEs but is not required.
# Output libname matches target name, with the usual extensions on your system
add_library(MyLibExample simple_lib.cpp simple_lib.hpp)
# Link each target with other targets or add options, etc.
# Adding something we can run - Output name matches target name
add_executable(MyExample simple_example.cpp)
# Make sure you link your targets with this command. It can also link libraries and
# even flags, so linking a target that does not exist will not give a configure-time error.
target_link_libraries(MyExample PRIVATE MyLibExample)
```

## 为 CMake 项目添加特性

```cmake
set(default_build_type "Release")
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
	message(STATUS "Setting build type to '${default_build_type}' as none was specified.")
	set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE
		STRING "Choose the type of build." FORCE)
	# Set the possible values of build type for cmake-gui
	set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
		"Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()
```

## 参考

- GitHub: <https://gitlab.com/CLIUtils/modern-cmake>
- Read Online: <https://cliutils.gitlab.io/modern-cmake>
