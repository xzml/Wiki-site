---
title: Bash技巧
toc: true
date: 2019-05-25 20:47:33
tags: [Unix, Bash]
categories:
---

## 文件与目录操作

### copy files except one

[BASH copy all files except one][https://stackoverflow.com/questions/1313590/bash-copy-all-files-except-one]

使用 `rsync`, 真的非常好用.

```bash
rsync -av from/ to/ --exclude=Default.png

-a, --archive               archive mode; equals -rlptgoD (no -H,-A,-X)
-v, --verbose               increase verbosity
```

实例: 当时我想将当前目录下的文件与目录拷贝到当前目录下的 Github 目录中, 使用:

```bash
rsync -av * Github --exclude=Github
```




## 参考资料
> - []()
> - []()
