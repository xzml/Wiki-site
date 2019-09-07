---
title: pybind11使用demo
toc: true
date: 2019-09-07 14:51:06
tags: [PyBind11]
categories:
---

为了让我们写的 C++ 代码能够在 Python 中被调用, pybind11 提供了大量 API 来实现这一目标. 本文给出一个 minimal demo 来介绍 pybind11 的基本使用方法.

## 文件结构介绍

这个 demo 包含如下的文件, 其中:

+ `utils` 目录中保存的是我们写的 C++ 程序;
+ `pybind` 目录则是利用 Pybind11 提供的功能来实现 Python 调用 C++ 程序;
+ `package.so` 是编译 `pybind/` 中的内容后得到的 Python 包, 可以使用 `import package` 调用;
+ `test.py` 用来测试 Python 是否能正常使用编译后的 `package.so`;
+ `Makefile` 用来组织整个工程.

```bash
.
├── Makefile
├── package.so
├── pybind
│   ├── add.cc
│   ├── subtract.cc
│   ├── main.cc
│   └── package.h
├── test.py
└── utils
    ├── add.cc
    └── add.h
```

下面依次介绍上面的列出的文件.

## C++ 程序

首先介绍底层 C++ 代码, 其中 `add.h` 中声明了几个简单的函数:

```cpp
#ifndef _UTILS_ADD_H
#define _UTILS_ADD_H

int add(int, int);
double add(double, double);
int subtract(int, int);

#endif
```

在 `add.cc` 中对这些函数进行实现:

```cpp
#include "add.h"

int add(int a, int b) {
    return a + b;
}

double add(double a, double b) {
    return a + b;
}

int subtract(int a, int b) {
    return a - b;
}
```

在写这些代码时, 我们无需考虑之后 Python 中如何调用, 只需要用 C++ 实现所需的功能. 之后专门创建一个文件夹, 比如 `pybind/`, 来处理 C++ 和 Python 交互的事宜, 以避免相互干扰.

## 利用 Pybind11 构建 Python 接口

`pybind` 的目录原本可以更为简单, 只用一个文件就可以实现所需的功能, 但按下面这种形式来划分, 代码结构会更为清晰. 其中 `add.cc` 和 `subtract.cc` 分别实现向 Python 提供加法和减法操作, 而 `main.cc` 实现在 Python 中提供 `package` 这个包. 此外这样组织代码还可以减少编译时间, 具体参考:

[How can I reduce the build time?](https://pybind11.readthedocs.io/en/stable/faq.html#how-can-i-reduce-the-build-time)


```bash
├── pybind
│   ├── add.cc
│   ├── subtract.cc
│   ├── main.cc
│   └── package.h
```

首先看 `package.h` 中的代码:

```cpp
#ifndef _PACKAGE_H
#define _PACKAGE_H

#include <pybind11/pybind11.h>
#include <pybind11/stl.h>
#include <pybind11/stl_bind.h>

#include "utils/add.h"

namespace py = pybind11;

void init_add(py::module &);
void init_sub(py::module &);

#endif
```

主要是导入 `pybind11` 的相关头文件, 并按照 [How can I reduce the build time?](https://pybind11.readthedocs.io/en/stable/faq.html#how-can-i-reduce-the-build-time) 中介绍的方式提供两个以 `py::module &` 作为参数的函数; 此外, 为了和前面写的 C++ 程序交互, 需要导入对应的头文件 `utils/add.h`.

再来看 `add.cc` 以及 `subtract.cc` 的实现:

```cpp
#include "package.h"

void init_add(py::module &m) {
    m.def("add", (int (*)(int, int))&add, "add two int numbers");
    m.def("add", (double (*)(double, double))&add, "add two double numbers");
}
```

以及

```cpp
#include "package.h"

void init_sub(py::module &m) {
    m.def("subtract", (int (*)(int, int))&subtract, "subtract two int numbers");
}
```

最后使用 `main.cc` 提供到 Python 的入口, build `package` 这个 module:

```cpp
#include "package.h"

PYBIND11_MODULE(package, m) {
    init_add(m);
    init_sub(m);
}
```

## 编写测试文件

如果 `package.so` 编译成功, 那么在 Python 中就能正常使用:

```python
import package

print(package.add(1, 2))
print(package.add(1.0, 2.0))
print(package.subtract(1, 2))
```

## 编译模块, Makefile 文件的编写

感觉写 C++ 代码最难的事情之一应该包括 Makefile 的编写, 先将代码贴出来, 再慢慢解析:

```bash
NAME := package
SRC := $(shell find pybind -name "*.cc")
TAR := $(NAME).so

INCLUDE_DIRS := $(dir .)
INCLUDE_DIRS += $(shell find utils -depth 0) 
INCLUDE_DIRS += $(shell find pybind -depth 0) 
CXX_SRCS := $(shell find utils -name "*.cc")
CXX_OBJS := $(CXX_SRCS:.cc=.o)

CXX := clang++
CXXFLAGS := -Wall -std=c++0x -shared -fPIC -Wl,-undefined,dynamic_lookup
CXXFLAGS += $(foreach includedir,$(INCLUDE_DIRS),-I$(includedir))
CXXFLAGS += $(foreach includedir,$(INCLUDE_DIRS),-L$(includedir))
CXXFLAGS += `python -m pybind11 --includes`
CXXLINKS :=

$(TAR) : $(SRC) $(CXX_OBJS)
	$(CXX) $(CXXFLAGS) -o $@ $^ $(CXXLINKS)

.PHONY : clean

clean :
	rm -rf $(CXX_OBJS) $(TAR)
```

+ `package.so` 依赖的文件是 `pybind/` 目录下的 pybind11 代码(使用 `$(SRC)` 表示)以及 `utils/` 中的 C++ 代码(使用 `$(CXX_OBJS)` 表示);
+ 根据 [跟我一起写 Makefile](https://seisman.github.io/how-to-write-makefile/Makefile.pdf) 9.3 节 "隐含规则使用的变量" 中的介绍, 像 `CXX`, `CXXFLAGS` 是 make 定义的隐含规则的变量, 需要注意.
+ 编译成 `.so` 文件需要使用 `-shared -fPIC`; 此外在 Mac 下编译还需要加上 `-Wl,-undefined,dynamic_lookup`(参考 [https://github.com/pybind/pybind11/issues/382](https://github.com/pybind/pybind11/issues/382));
+ `$(CXX_OBJS)` 会通过 make 的隐含规则进行编译, 它们是 `utils/` 目录下 C++ 代码编译后的目标文件;
+ 最后用 `$(CXX) $(CXXFLAGS) -o $@ $^ $(CXXLINKS)` 编译并链接生成 `package.so`.

## 运行测试文件

运行测试文件 `test.py`, 能正常使用.

```bash
3                                                                                                                  │  ~
3.0                                                                                                                │  ~
-1
```


## 参考资料
> - [跟我一起写 Makefile](https://seisman.github.io/how-to-write-makefile/Makefile.pdf)
> - [How can I reduce the build time?](https://pybind11.readthedocs.io/en/stable/faq.html#how-can-i-reduce-the-build-time)
> - [https://github.com/pybind/pybind11/issues/382](https://github.com/pybind/pybind11/issues/382)
