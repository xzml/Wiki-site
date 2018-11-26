---
title: Mac下使用Hexo+Github搭建个人博客
toc: true
date: 2018-11-25 22:58:43
tags: [Tricks]
categories:
---

## 准备工作

建议看看参考博客中的第一个, 图文并茂, 可以感受到很多细节, 作者非常用心, 第二个参考博客是更新奇的内容, 包括插入图片, 购买域名等.

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

1. 首先在 Github 上创建名为 `<user>.github.io` 的 repository, 我的 `<user>` 就是 `xzml`.
2. 然后在 `PWiki/_config.yml` 文件的 `deploy` 项下, 增加如下内容:

```bash
deploy:
  type: git
  repository: git@github.com:xzml/xzml.github.io.git
  branch: master
```

3. 安装 Github 部署器:

```bash
npm install hexo-deployer-git --save
```

否则应该会出现 `ERROR Deployer not found: git` 错误.

4. 将静态博客部署到 Github 上:

```bash
hexo deploy # 缩写 hexo d
```

## 更换主题

按照 [https://github.com/zthxxx/hexo-theme-Wikitten](https://github.com/zthxxx/hexo-theme-Wikitten) 中的 `README.md` 操作就可以了.

大致思路就是:

+ 从 Github 上将主题下载到 `PWiki/` 下的 `themes` 目录下
+ 修改 `PWiki/themes/Wikitten/_config.yml` 文件, 对主题进行配置
+ 修改 `PWiki/_config.yml` 文件, 对项目进行配置, 比如将 `theme` 这一项更改为 `Wikitten`.

另外需要注意的是 Wikitten 主题需要额外的一些插件, `README.md` 中写了, 为了安装它们, 我在 `PWiki/package.json` 直接增加了如下部分:

```json
"dependencies": {
    "hexo": "^3.7.0",
    "hexo-autonofollow": "^1.0.1",
    "hexo-deployer-git": "^0.3.1",
    "hexo-directory-category": "^1.0.5",
    "hexo-generator-archive": "^0.1.5",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-feed": "^1.2.2",
    "hexo-generator-index": "^0.2.1",
    "hexo-generator-json-content": "^3.0.1",
    "hexo-generator-sitemap": "^1.2.0",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-marked": "^0.3.2",
    "hexo-renderer-stylus": "^0.3.3",
    "hexo-server": "^0.3.1"
}
```

然后再使用 `npm install` 命令安装即可. 还有要注意修改主题的 `_config.yml` 文件, 将原作者的个人信息改成自己的信息.

## 保存博客到仓库

我们自己使用 `hexo new <title>` 产生的博客源文件一般是放在 `PWiki/source/_posts/` 目录下, 使用 `hexo generate` 命令产生的静态页面一般是放在 `PWiki/publish/` 文件夹下. 为了对博客的 Markdown 源文件以及博客使用的主题进行备份, 参考 Wikitten 原作者的方法, 我在 Github 上创建了一个名为 `Wiki-site` 的仓库用于存储这些源文件.

具体方法是:

+ 在 Github 上创建一个名为 `Wiki-site` 的新仓库;
+ 在本地上的 `PWiki/` 目录下使用 `git init` 命令;
+ 修改 `PWiki/` 项目中的 `.gitignore` 文件, 过滤掉那些不需要追踪版本的文件; 另外要注意的问题是, 由于 `PWiki/themes/Wikitten` 是个 git submodule, 在 `PWiki/` 目录下直接使用 `git add *` 之类的命令可能会引起一些 Warning, 我的做法是到 `PWiki/themes/Wikitten/` 目录下先用 `git add/commit` 等命令处理好后, 再回到 `PWiki/` 目录使用 `git add/commit` 命令;
+ 回到 `PWiki/` 目录, 将上面的修改都 `git add/commit`;
+ 将本地项目与远程服务器进行关联, `git remote add origin git@github.com:xzml/Wiki-site.git` (具体可以参考第 5 个博客)
+ 执行 `git pull origin master`, 将远程服务器中的 master 分支和本地的 origin 合并, 防止冲突.
+ 上面没有报错之后, 可以使用 `git status` 查看当前本地仓库是不是干净的, 如果是的话, 就可以使用 `git push -u origin master` 推送到 Github 上了.
+ 上面的步骤其实已经将问题给搞定了, 这里记录一个我出现的问题: 我有多个 Github 账号, 即使设置了 `PWiki/` 的 local user 为 xzml, 在使用 `git push -u origin master` 时, 仍会使用另一个 userb. 由于我访问 Github 使用的是 SSH, 这说明访问 `git@github.com` 时使用的是 userb, 为了使通过 SSH 访问 `git@github.com` 使用 xzml, 我直接修改 `PWiki/.git/config` 文件中的 `url = xzml:xzml/Wiki-site.git`, 这是因为我在 `.ssh/config` 给通过 xzml 访问 `git@github.com` 这一行为设置了别名为 `xzml`.

## 参考资料
> - [Mac 下 Hexo 和 GitHub-Pages 搭建个人博客（一）](http://lijiankun24.com/Mac%E4%B8%8BHexo%E5%92%8CGitHub-Pages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A21/)
> - [Mac 下 Hexo 和 GitHub-Pages 搭建个人博客（二）](http://lijiankun24.com/Mac%E4%B8%8BHexo%E5%92%8CGitHub-Pages%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A22/)
> - [macOS环境下利用Github和Hexo部署博客](https://www.jianshu.com/p/1519f22aff24)
> - [使用 Hexo 生成静态博客过程记录](https://blog.zthxxx.me/posts/Hexo-Build-Static-Blog-Process/)
> - [使用Git命令把本地项目上传到Github托管](https://blog.csdn.net/CHENYUFENG1991/article/details/48930471)
