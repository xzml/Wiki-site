---
title: Mac下使用Hexo+Github搭建个人博客
toc: true
date: 2018-11-25 22:58:43
tags: [Tricks]
categories:
---

## 准备工作

建议看看参考博客中的第一个, 图文并茂, 可以感受到很多细节, 作者非常用心.

### 安装 Nodejs 和 npm

Hexo 是用 Nodejs 写成了, 所以需要先安装 Nodejs 和 npm, 推荐使用 Homebrew 安装. 安装完 Nodejs 后建议设置 [npm 淘宝镜像](http://npm.taobao.org/).

```bash
brew install node
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global

# 查看版本号
node -v
npm -v
```

### 安装 Hexo

```bash
npm install -g hexo

# 查看版本号
hexo -v
```

### 本地调试

创建某个目录用于存放你的博客, 比如我创建了 `PWiki/` 文件夹用于存放博客相关的文件, 然后执行如下命令:

```bash
hexo init # 首先初始化 Hexo, 会将相关的文件从 Github 上下载下来, 默认使用 landscape 主题
npm install # 在部署博客之前需要安装依赖项, 我想这个命令会直接读取 PWiki/package.json 文件中的内容
hexo generate # 生成静态页面, Hexo 是一个博客框架, 只有执行了 generate 命令才能生成具体的 html, css 等文件
hexo server # 启动服务, 用于本地调试
hexo clean # 可以清除已经产生的静态页面, 如 PWiki/publish 目录
hexo new # 创建新博客
```

+ 本地打开 `http://localhost:4000` 进行效果查看
+ 命令缩写:

```bash
hexo generate # 缩写 hexo g
hexo server # 缩写 hexo s
hexo new # 缩写 hexo n
```


## 部署到 Github

+ 首先在 Github 上创建名为 `<user>.github.io` 的 repository, 我的 `<user>` 就是 `xzml`.
+ 然后在 `PWiki/_config.yml` 文件的 `deploy` 项下, 增加如下内容:

```bash
deploy:
  type: git
  repository: git@github.com:xzml/xzml.github.io.git
  branch: master
```

+ 安装 Github 部署器:

```bash
npm install hexo-deployer-git --save
```

否则应该会出现 `ERROR Deployer not found: git` 错误.

+ 将静态博客部署到 Github 上:

```bash
hexo deploy # 缩写 hexo d
```


## 参考资料
> - [Mac 下 Hexo 和 GitHub-Pages 搭建个人博客 (一)](http://lijiankun24.com/Mac%E4%B8%8BHexo%E5%92%8CGitHub-Pages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A21/)
> - [macOS环境下利用Github和Hexo部署博客](https://www.jianshu.com/p/1519f22aff24)
> - [使用 Hexo 生成静态博客过程记录](https://blog.zthxxx.me/posts/Hexo-Build-Static-Blog-Process/)
