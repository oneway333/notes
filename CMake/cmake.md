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

- CMake 使用变量来存储和传递信息，这些**变量**可以在 CMakeLists.txt 文件中定义和使用。
  - 变量主要看作用范围和所谓的生命周期了吧
- 变量可以分为**普通变量和缓存变量**。

### 2.1、变量定义与使用

**定义变量：**

```cmake
set(MY_VAR "Hello World")
```

**使用变量：**

```cmake
message(STATUS "Variable MY_VAR is ${MY_VAR}")
```

### 2.2、缓存变量

- 缓存变量存储在 CMake 的**缓存文件**中，用户可以在 CMake 配置时修改这些值。缓存变量通常用于用户输入的设置，例如**编译选项和路径**。

**定义缓存变量：**

```cmake
set(MY_CACHE_VAR "DefaultValue" CACHE STRING "A cache variable")
```

**使用缓存变量：**

```cmake
message(STATUS "Cache variable MY_CACHE_VAR is ${MY_CACHE_VAR}")
```

## 3、查找库和包

- CMake 可以通过 **find_package()** 指令**自动**检测和配置外部库和包。

常用于查找系统安装的库或第三方库。

### 3.1、find_package() 指令

基本用法：

```cmake
find_package(Boost REQUIRED)
```

指定版本：

```cmake
find_package(Boost 1.70 REQUIRED)
```

查找库并指定路径：

```cmake
find_package(OpenCV REQUIRED PATHS /path/to/opencv)
```

使用查找到的库：

```cmake
target_link_libraries(MyExecutable Boost::Boost)
```

设置包含目录和链接目录：

```cmake
include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})
```

### 3.2、使用第三方库

- 假设你想在项目中使用 Boost 库，CMakeLists.txt 文件可能如下所示：

- ```cmake
  cmake_minimum_required(VERSION 3.10)
  project(MyProject CXX)
  
  # 查找 Boost 库
  find_package(Boost REQUIRED)
  
  # 添加源文件
  add_executable(MyExecutable main.cpp)
  
  # 链接 Boost 库
  target_link_libraries(MyExecutable Boost::Boost)
  ```

- 通过上述内容，用户可以了解 CMakeLists.txt 文件的基本结构和常用指令，掌握如何定义和使用变量，查找和配置外部库，从而能够有效地使用 CMake 管理项目构建过程。



# 三、CMake 构建流程

CMake 的构建流程分为几个主要步骤，从设置项目到生成和执行构建命令。

1. **创建构建目录**：保持源代码目录整洁。
   1. 不就是新建一个文件夹。。。

2. **使用 CMake 生成构建文件**：配置项目并生成适合平台的构建文件。
3. **编译和构建**：使用生成的构建文件执行编译和构建。
4. **清理构建文件**：删除中间文件和目标文件。
5. **重新配置和构建**：处理项目设置的更改。

![](E:\Git\notes\CMake\imgs\cmake-simple-flowchart.png)

以下是详细的构建流程说明：

### 1、创建构建目录

- CMake 推荐使用 **"Out-of-source"** 构建方式，即将构建文件放在源代码目录之外的独立目录中。
- 这样可以保持源代码目录的整洁，并方便管理不同的构建配置。

![](E:\Git\notes\CMake\imgs\Out_of_Source-build.png)

- **创建构建目录：**在项目的根目录下，创建一个新的构建目录。例如，可以创建一个名为 build 的目录。

  ```shell
  mkdir build
  ```

- **进入构建目录：**进入刚刚创建的构建目录。

  ```shell
  cd build
  ```

### 2、使用 CMake 生成构建文件

- 在构建目录中运行 CMake，以生成适合当前平台的构建系统文件（例如 Makefile、Ninja 构建文件、Visual Studio 工程文件等）。
- **运行 CMake 配置：**在构建目录中运行 CMake 命令，指定源代码目录。源代码目录是包含 CMakeLists.txt 文件的目录。

```shell
cmake ..
```

- 如果需要**指定生成器**（如 Ninja、Visual Studio），可以使用 -G 选项。例如：

```shell
cmake -G "Ninja" ..
```

- 如果需要指定构建类型（如 Debug 或 Release），可以使用 -DCMAKE_BUILD_TYPE 选项。例如：

```shell
cmake -DCMAKE_BUILD_TYPE=Release ..
```

- **检查配置结果：**CMake 会**输出**配置过程中的详细信息，包括找到的库、定义的选项等，如果没有错误，构建系统文件将被生成到构建目录中。

### 3、编译和构建

- 使用生成的构建文件进行编译和构建。
- 不同的构建系统使用不同的命令。
- **使用 Makefile（或类似构建系统）：**如果使用 Makefile，可以运行 make 命令来编译和构建项目。

```shell
make
```

- 如果要构建特定的目标，可以指定目标名称。例如：

```shell
make MyExecutable
```

- **使用 Ninja：**如果使用 Ninja 构建系统，运行 ninja 命令来编译和构建项目。

```shell
ninja
```

- 与 make 类似，可以构建特定的目标：

```shell
ninja MyExecutable
```

- **使用 Visual Studio：**如果生成了 Visual Studio 工程文件，可以打开 **.sln** 文件，然后在 Visual Studio 中选择构建解决方案。
- 也可以使用 msbuild 命令行工具来编译：

```shell
msbuild MyProject.sln /p:Configuration=Release
```

### 4、清理构建文件

- 构建过程中生成的中间文件和目标文件可以通过清理操作删除。
- **使用 Makefile：**运行 make clean 命令（如果定义了清理规则）来删除生成的文件。

```shell
make clean
```

- **使用 Ninja：**运行 ninja clean 命令（如果定义了清理规则）来删除生成的文件。

```shell
ninja clean
```

- **手动删除：**可以手动删除构建目录中的所有文件，但保留源代码目录不变。例如：

```shell
rm -rf build/*
```

### 5、重新配置和构建

- 如果修改了 CMakeLists.txt 文件或项目设置，可能需要重新配置和构建项目。
- **重新运行 CMake 配置：**在构建目录中重新运行 CMake 配置命令。

```shell
cmake ...
```

- **重新编译：**使用构建命令重新编译项目。

```shell
make
```

# 四、CMake 构建实例

CMake 构建步骤如下：

1. **创建 `CMakeLists.txt` 文件**：定义项目、目标和依赖。
2. **创建构建目录**：保持源代码目录整洁。
3. **配置项目**：使用 CMake 生成构建系统文件。
4. **编译项目**：使用构建系统文件编译项目。
5. **运行可执行文件**：执行生成的程序。
6. **清理构建文件**：删除中间文件和目标文件。

假设我们有一个简单的 C++ 项目，包含一个主程序文件和一个库文件，我们将使用 CMake 构建这个项目。

我们的项目结构如下：

```shell
MyProject/
├── CMakeLists.txt
├── src/
│   ├── main.cpp
│   └── mylib.cpp
└── include/
    └── mylib.h
```

- `main.cpp`：主程序源文件。
- `mylib.cpp`：库源文件。
- `mylib.h`：库头文件。
- `CMakeLists.txt`：CMake 配置文件。

### 1、创建 CMakeLists.txt 文件

在 MyProject 目录下创建 CMakeLists.txt 文件。

CMakeLists.txt 文件用于配置 CMake 项目。

**CMakeLists.txt 文件内容：**

```cmake
cmake_minimum_required(VERSION 3.10)   # 指定最低 CMake 版本

project(MyProject VERSION 1.0)          # 定义项目名称和版本

# 设置 C++ 标准为 C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 添加头文件搜索路径
include_directories(${PROJECT_SOURCE_DIR}/include)

# 添加源文件
add_library(MyLib src/mylib.cpp)        # 创建一个库目标 MyLib
add_executable(MyExecutable src/main.cpp)  # 创建一个可执行文件目标 MyExecutable

# 链接库到可执行文件
target_link_libraries(MyExecutable MyLib)
```

**说明：**

- **`cmake_minimum_required(VERSION 3.10)`**：指定 CMake 的最低版本为 3.10。
- **`project(MyProject VERSION 1.0)`**：定义项目名称为 `MyProject`，版本为 1.0。
- **`set(CMAKE_CXX_STANDARD 11)`**：指定 C++ 标准为 C++11。
- **`include_directories(${PROJECT_SOURCE_DIR}/include)`**：指定头文件目录。
- **`add_library(MyLib src/mylib.cpp)`**：创建一个名为 `MyLib` 的库，源文件是 `mylib.cpp`。
- **`add_executable(MyExecutable src/main.cpp)`**：创建一个名为 `MyExecutable` 的可执行文件，源文件是 `main.cpp`。
- **`target_link_libraries(MyExecutable MyLib)`**：将 `MyLib` 库链接到 `MyExecutable` 可执行文件。
