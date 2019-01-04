---
title: Make Life Easier
toc: true
date: 2019-01-03 10:22:30
tags: [Tricks]
categories:
---

## 我现在发现, 为什么不用 Docker ...

是的, 赶紧把 Docker 用熟来. 装好 Docker 和 Nvidia-docker, 然后使用 sshfs 将远程服务器的文件映射到本地, 嗯, 真香~

* [Docker Documentation](https://docs.docker.com/install/)
* [NVIDIA-docker Documentation](https://github.com/NVIDIA/nvidia-docker)

## 安装 oh-my-zsh


## Pyenv 管理多版本的 Python

1. 安装 Pyenv

主页在: [https://github.com/pyenv/pyenv](https://github.com/pyenv/pyenv), Mac 用户可以使用 Homebrew 安装, 此时安装的路径在:

```bash
/usr/local/Cellar/pyenv
```

而 Ubuntu 用户可以使用如下一些命令安装 (先把 oh-my-zsh 装好)

```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
exec $SHELL
```

2. 加快 Python/Anaconda 的下载速度

要使各个版本的发行版加快下载, 可以修改为清华镜像, 比如我的 `anaconda3-4.2.0` 文件如下, 其中 `https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/` 是重点, 还有后面一大堆估计是编码的东西也给删掉了.

首先到 Pyenv 的如下目录:


```bash
pyenv_root='~/.pyenv' # Ubuntu
pyenv_root=/usr/local/Cellar/pyenv # Mac
$(pyenv_root)/1.2.7/plugins/python-build/share/python-build
```

然后修改 `anaconda3-4.2.0` 文件, 这里给的例子是修改 OSX 系统的:

```bash
case "$(anaconda_architecture 2>/dev/null || true)" in
"Linux-x86" )
  install_script "Anaconda3-4.2.0-Linux-x86" "https://repo.continuum.io/archive/Anaconda3-4.2.0-Linux-x86.sh#1a8320635f2f06ec9d8610e77d6d0f9cb2c5d11d20a4ff7fcda113e04b0a8a50" "anaconda" verify_py35
  ;;
"Linux-x86_64" )
  install_script "Anaconda3-4.2.0-Linux-x86_64" "https://repo.continuum.io/archive/Anaconda3-4.2.0-Linux-x86_64.sh#73b51715a12b6382dd4df3dd1905b531bd6792d4aa7273b2377a0436d45f0e78" "anaconda" verify_py35
  ;;
"MacOSX-x86_64" )
  #install_script "Anaconda3-4.2.0-MacOSX-x86_64" "https://repo.continuum.io/archive/Anaconda3-4.2.0-MacOSX-x86_64.sh#95448921601e1952e01a17ba9767cd3621c154af7fc52dd6b7f57d462155a358" "anaconda" verify_py35
  install_script "Anaconda3-4.2.0-MacOSX-x86_64" "https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-4.2.0-MacOSX-x86_64.sh" "anaconda" verify_py35
  ;;
* )
  { echo
    colorize 1 "ERROR"
    echo ": The binary distribution of Anaconda3 is not available for $(anaconda_architecture 2>/dev/null || true)."
    echo
  } >&2
  exit 1
  ;;
esac
```

## 修改 Pip 的镜像

修改 `~/.pip/pip.conf` 文件如下:

```bash
[global]
index-url=http://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```

## conda 设置清华镜像

[Anaconda | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirror.tuna.tsinghua.edu.cn/help/anaconda/)

```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```





## 参考资料
> - []()
> - []()
