# 一、CMake 教程

- CMake 是个一个开源的跨平台自动化建构系统，用来管理软件建置的程序，并不依赖于某特定编译器，并可支持多层目录、多个应用程序与多个函数库。
- CMake 通过使用简单的配置文件 CMakeLists.txt，自动生成不同平台的构建文件（如 Makefile、Ninja 构建文件、Visual Studio 工程文件等），**简化了**项目的编译和构建过程。
  - 可能大概真的是一种简化吧，当然简化了就可以不需要那么多人了，哈哈哈哈
- CMake 本身不是构建工具，而是生成构建系统的工具，它生成的构建系统可以使用不同的编译器和工具链。

## 1、基本工作流程

1. **编写 CMakeLists.txt 文件：** 定义项目的构建规则和依赖关系。
   - 在txt中写代码
2. **生成构建文件：** 使用 CMake 生成适合当前平台的构建系统文件（例如 Makefile、Visual Studio 工程文件）。
   - 用cmake进行一种其实是叫做编译的动作
3. **执行构建：** 使用生成的构建系统文件（如 `make`、`ninja`、`msbuild`）来编译项目。
   - 调用平台的接口来实现真正的构建

![](E:\Git\notes\CMake\imgs\Single_Source_Build-cmake.png)

## 2、相关链接

- CMake 官网 https://cmake.org/
- CMake 官方文档：https://cmake.org/cmake/help/latest/guide/tutorial/index.html
- CMake 源码：https://github.com/Kitware/CMake
- CMake 源码：https://gitlab.kitware.com/cmake/cmakeku

# 二、CMake 基础

## 1、CMakeLists.txt 文件

CMakeLists.txt 是 CMake 的配置文件，用于定义项目的构建规则、依赖关系、编译选项等。

每个 CMake 项目通常都有一个或多个 CMakeLists.txt 文件。

### 1.1、文件结构和基本语法

- CMakeLists.txt 文件使用一系列的 CMake 指令来描述构建过程。

- 常见的指令包括：

  1、指定 CMake 的最低版本要求：

  ```cmake
  cmake_minimum_required(VERSION <version>)
  ```

  例如：

  ```cmake
  cmake_minimum_required(VERSION 3.10)
  ```

  2、定义项目的名称和使用的编程语言：

  ```cmake
  project(<project_name> [<language>...])
  ```

  例如：

  ```cmake
  project(MyProject CXX)
  ```

  3、指定要生成的可执行文件和其源文件：

  ```cmake
  add_executable(<target> <source_files>...)
  ```

  例如：

  ```cmake
  add_executable(MyExecutable main.cpp other_file.cpp)
  ```

  4、创建一个库（静态库或动态库）及其源文件：

  ```cmake
  add_library(<target> <source_files>...)
  ```

  例如：

  ```cmake
  add_library(MyLibrary STATIC library.cpp)
  ```

  5、链接目标文件与其他库：

  ```cmake
  target_link_libraries(<target> <libraries>...)
  ```

  例如：

  ```cmake
  target_link_libraries(MyExecutable MyLibrary)
  ```

  6、添加头文件搜索路径：

  ```cmake
  include_directories(<dirs>...)
  ```

  例如：

  ```cmake
  include_directories(${PROJECT_SOURCE_DIR}/include)
  ```

  7、设置变量的值：

  ```cmake
  set(<variable> <value>...)
  ```

  例如：

  ```cmake
  set(CMAKE_CXX_STANDARD 11)
  ```

  8、设置目标属性：

  ```cmake
  target_include_directories(TARGET target_name
                            [BEFORE | AFTER]
                            [SYSTEM] [PUBLIC | PRIVATE | INTERFACE]
                            [items1...])
  ```

  例如：

  ```cmake
  target_include_directories(MyExecutable PRIVATE ${PROJECT_SOURCE_DIR}/include)
  ```

  9、安装规则：

  ```cmake
  install(TARGETS target1 [target2 ...]
          [RUNTIME DESTINATION dir]
          [LIBRARY DESTINATION dir]
          [ARCHIVE DESTINATION dir]
          [INCLUDES DESTINATION [dir ...]]
          [PRIVATE_HEADER DESTINATION dir]
          [PUBLIC_HEADER DESTINATION dir])
  ```

  例如：

  ```cmake
  install(TARGETS MyExecutable RUNTIME DESTINATION bin)
  ```

  10、条件语句 (if, elseif, else, endif 命令)

  ```cmake
  if(expression)
    # Commands
  elseif(expression)
    # Commands
  else()
    # Commands
  endif()
  ```

  例如：

  ```cmake
  if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    message("Debug build")
  endif()
  ```

  11、自定义命令 (add_custom_command 命令)：

  ```cmake
  add_custom_command(
     TARGET target
     PRE_BUILD | PRE_LINK | POST_BUILD
     COMMAND command1 [ARGS] [WORKING_DIRECTORY dir]
     [COMMAND command2 [ARGS]]
     [DEPENDS [depend1 [depend2 ...]]]
     [COMMENT comment]
     [VERBATIM]
  )
  ```

  例如：

  ```cmake
  add_custom_command(
     TARGET MyExecutable POST_BUILD
     COMMAND ${CMAKE_COMMAND} -E echo "Build completed."
  )
  ```

- 一个简单的 CMakeLists.txt 文件示例：

  ```cmake
  cmake_minimum_required(VERSION 3.10)
  project(MyProject CXX)
  
  # 添加源文件
  add_executable(MyExecutable main.cpp)
  
  # 设置 C++ 标准
  set(CMAKE_CXX_STANDARD 11)
  ```

## 2、变量和缓存

1.2、变量和缓存

  CMake 使用变量来存储和传递信息，这些变量可以在 CMakeLists.txt 文件中定义和使用。

  变量可以分为普通变量和缓存变量。

### 变量定义与使用

  **定义变量：**



# CMake 构建流程

CMake 的构建流程分为几个主要步骤，从设置项目到生成和执行构建命令。

1. **创建构建目录**：保持源代码目录整洁。
2. **使用 CMake 生成构建文件**：配置项目并生成适合平台的构建文件。
3. **编译和构建**：使用生成的构建文件执行编译和构建。
4. **清理构建文件**：删除中间文件和目标文件。
5. **重新配置和构建**：处理项目设置的更改。

