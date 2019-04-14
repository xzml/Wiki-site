---
title: PyTorch-C++ 接口
toc: true
date: 2019-04-14 12:01:00
tags: [PyTorch, C++]
categories:
---

## 介绍

从 `https://pytorch.org/` 下载 LibTorch 代码

## Hello World

写一个 Hello World 程序

```c++
#include <iostream>
#include <torch/torch.h>

using namespace std;

int main() {
    torch::Tensor tensor = torch::rand({2, 2});
    cout << tensor << endl;
}
```

## 编译

Makefile 如下:

其中 `.../libtorch` 中保存着 LibTorch 的代码

```bash
TORCH = /Users/zhang/Codes/C++/pytorch-c++/libtorch
CC = clang++
CFLAGS = -Wall -std=c++0x
CFLAGS += -I$(TORCH)/include -L$(TORCH)/lib
CFLAGS += -I$(TORCH)/include/torch/csrc/api/include
CLINKS = -ltorch.1 -lcaffe2 -lc10


NAME = ex1
SRC = $(NAME).cpp
TAR = $(NAME).out

$(TAR) : $(SRC)
	$(CC) $(CFLAGS) -o $@ $^ $(CLINKS)

.PHONY : run clean

run :
	LD_LIBRARY_PATH=$(TORCH)/lib ./$(TAR)

clean :
	rm -rf *.out
```





## 参考资料
> - []()
> - []()
