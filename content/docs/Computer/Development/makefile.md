---
linkTitle: Makefile 编写
title: Makefile 编写
---

## 一、基本概念
### 规则
规则是 Makefile 的核心，用于定义如何从依赖文件生成目标文件，以及执行操作的条件。它包含目标、依赖和命令三个关键部分：
- **目标（Target）**：规则的生成结果，可以是可执行文件、目标文件、文档等。例如 `main: main.o utils.o` 中，`main` 就是目标，目标数量可以是一个或多个。
- **依赖（Dependencies）**：生成目标所需的文件或其他目标。在上述例子中，`main.o` 和 `utils.o` 是 `main` 的依赖，依赖可以是源文件、头文件、库文件等，且相互间可能存在依赖关系。
- **命令（Commands）**：描述从依赖生成目标的具体操作，必须以 Tab 键开头。例如 `gcc -o main main.o utils.o`。Make 会按顺序执行命令，并根据目标和依赖的时间戳判断是否重新生成目标。若目标不存在或依赖文件更新，就会执行命令更新目标。

## 二、常用特殊符号和选项

### （一）自动变量
- **`$<`**：代表规则依赖列表中的第一个依赖文件。在 `main: main.o utils.o` 规则里，若命令为 `gcc -o $@ $<`，`$<` 表示 `main.o`。
- **`$@`**：代表规则的目标文件名。在上述规则中，`$@` 代表 `main`。

### （二）`-o` 选项
`-o` 是编译器或链接器命令的选项，用于指定输出文件的名称。
- **编译器（如 gcc）**：`gcc -o my_program main.c` 会将 `main.c` 编译成名为 `my_program` 的可执行文件；`gcc -c -o main.o main.c` 会将 `main.c` 编译成名为 `main.o` 的目标文件。
- **归档工具（ar）**：`ar rcs -o libmylib.a main.o utils.o` 会将 `main.o` 和 `utils.o` 打包成名为 `libmylib.a` 的静态库，实际使用时 `ar` 命令的 `-o` 选项通常可省略。

### （三）`.PHONY`
`.PHONY` 用于声明伪目标，伪目标不是实际文件，而是组织命令执行的机制。声明为伪目标后，Make 工具不会检查文件是否存在或更新，每次都会直接执行目标对应的命令。
- **避免与同名文件冲突**：若项目目录有 `clean` 文件，未声明 `.PHONY` 时，`make clean` 可能不执行命令。声明后：
```makefile
.PHONY: clean
clean:
    rm -f *.o my_program
```
无论 `clean` 文件是否存在，`make clean` 都会执行清理命令。
- **定义抽象操作**：如 `all` 目标用于一次性构建项目内容，本身不对应具体文件。通过 `.PHONY` 声明，可确保 `make all` 按顺序执行依赖子目标的命令：
```makefile
.PHONY: all
all: program1 program2

program1:
    gcc -o program1 program1.c

program2:
    gcc -o program2 program2.c
```
- **方便执行辅助操作**：常见伪目标如 `install` 用于安装程序，`test` 用于运行测试用例。声明后可保证每次正常执行：
```makefile
.PHONY: install test

install:
    cp my_program /usr/local/bin

test:
    ./my_program --test
```

### （四）`all:` 目标
`all:` 通常作为 Makefile 的默认目标。执行 `make` 命令且不指定目标时，Make 工具会查找 `all:` 目标并执行其对应命令。它可组合多个目标，方便一次性完成相关操作，为用户提供统一入口。例如：
```makefile
all: clean build

clean:
    rm -f *.o

build:
    gcc -o main main.c
``` 

### （五）VPATH变量

#### `VPATH` 的作用
`VPATH` 是 `Make` 工具中的一个特殊变量，其用途是指定 `Make` 在查找依赖文件时所搜索的路径。当 `Make` 要查找某个依赖文件（像源文件），但在当前目录下找不到时，就会到 `VPATH` 所指定的路径里去搜索。

#### 在当前 `Makefile` 中的体现
在这个 `Makefile` 中，有如下设置：
```makefile
VPATH = src
```
这意味着当 `Make` 需要查找依赖文件时，若在当前目录下找不到，就会到 `src` 目录中去查找。具体而言，在规则：
```makefile
bin/spmv_spv8 : spmv_spv8.cpp
	$(CXX) $(CFLAGS) $< -o $@

bin/spmv_mkl : spmv_mkl.cpp
	$(CXX) $(CFLAGS) $< -o $@
```
里，`Make` 要编译 `spmv_spv8.cpp` 和 `spmv_mkl.cpp` 这两个源文件。要是这两个文件不在当前目录下，`Make` 就会依据 `VPATH` 的设置，到 `src` 目录中去查找这两个文件。

#### 示例说明
假设当前目录结构如下：
```
spv8-public-master/
├── Makefile
└── src/
    ├── spmv_spv8.cpp
    └── spmv_mkl.cpp
```
当你在 `spv8-public-master` 目录下执行 `make` 命令时，`Make` 会尝试编译 `spmv_spv8.cpp` 和 `spmv_mkl.cpp`。由于当前目录下没有这两个文件，`Make` 会根据 `VPATH = src` 的设置，到 `src` 目录中查找这两个文件，然后进行编译。

#### 总结
在这个 `Makefile` 中，`VPATH` 是有用的，它让 `Make` 能够在 `src` 目录中查找源文件，从而顺利完成编译任务。这样可以使项目的目录结构更加清晰，源文件能够统一存放在 `src` 目录中，而 `Makefile` 则可以放在项目的根目录下。 