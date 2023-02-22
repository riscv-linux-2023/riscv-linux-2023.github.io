---
layout: post
title:  "《CMake Best Practices》读书笔记"
date:   2023-02-22 13:52:59 +0800
categories: book cmake
---

## 基础知识

### 安装

<https://cmake.org/download/>

### Demo

代码结构：

```bash
demo/
├── CMakeLists.txt
└── src
    └── main.c

1 directory, 2 files
```

`CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.21)

project(
        demo_executable
        VERSION 1.0
        DESCRIPTION "A simple C project to demonstrate basic CMake usage"
        LANGUAGES C)

add_executable(demo)
target_sources(demo PRIVATE src/main.c)
```

`main.c`

```c
#include <stdio.h>

int main() {
        printf("Hello CMake!\n");
}
```

运行：

```bash
cd demo
mkdir build
cd build
cmake ..
cmake --build .
./demo
# Hello CMake!
```

## MakeLists.txt

内置变量

* PROJECT_DESCRIPTION: 项目的描述字符串
* PROJECT_HOMEPAGE_URL: 项目的URL字符串
* PROJECT_VERSION: 项目的完整版本信息
* PROJECT_VERSION_MAJOR: 版本字符串的第一个数字
* PROJECT_VERSION_MINOR: 版本字符串的第二个数字
* PROJECT_VERSION_PATCH: 版本字符串的第三个数字
* PROJECT_VERSION_TWEAK: 版本字符串的第四个数字

1. 变量
	* set(YEAR 2023)
	* unset(YEAR)
	* message(STATUS "It's ${YEAR}")
2. 列表
	* set(MYLIST hello world)
	* set(MYLIST "hello;world")
	* list(APPEND MYLIST "cmake")
3. 属性
	* set_property
	* get_property
4. 循环和条件
	* if(DEFINED MY_VAR)
	* if(MYVAR STREQUAL "FOO")
	* while(MYVAR LESS "5")
	* endwhile()
	* foreach(ITEM IN LISTS MYLIST)
	* endforeach()
	* foreach(ITEM RANGE 0 10)
	* endforeach()
5. 函数
	* function(foo ARG1)
	* endfunction()
	* foo("bar")
6. 宏
	* macro()
	* endmacro()
7. 目标
	* add_executable
	* add_library
	* add_custom_target
8. 生成器表达式
	* $<LOWER_CASE:CMake>
9. CMake 策略
	* cmake_minimum_required

## CMake 的最佳使用方式

### 命令行

```
CMake \
	-G "Unix Makefiles" \
	-DCMAKE_BUILD_TYPE:STRING=Release \
	-S . \
	-B ./build \
	--clean-first
	-- --trace
```

> VERSION not allowed unless CMP0048 is set to NEW

```cmake
cmake_minimum_required(VERSION 3.25)
```


### VIsual Studio Code

CMake Tools

`F1`

- *cmake quick start*
- *CMake: Configure*
- *Cmake: Build*
- *Cmake: Clean*

`Debug`

- *CMake: Set debug target*
- *CMake: Debug(Ctrl+F5)*
- *CMake: run without Debugging(Shift+F5)*

`参数`

- `.vscode/settings.json`

```json
{
    "cmake.debugConfig": {
    "args": [
        "Hello"
    ]
}
```

### Doxygen

```bash
sudo apt install doxygen
sudo apt install graphviz
sudo apt install openjdk-17-jre-headless
```

> Could not find PLANTUML_JAR_PATH using the following files: plantuml.jar

```bash
wget -c https://github.com/plantuml/plantuml/releases/download/v1.2023.1/plantuml-1.2023.1.jar
sudo cp plantuml-1.2023.1.jar /usr/bin/plantuml.jar
```

## 参考

- GitHub: <https://github.com/xiaoweiChen/CMake-Best-Practices>
- Code: <https://github.com/PacktPublishing/CMake-Best-Practices>
