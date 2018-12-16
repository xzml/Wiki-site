---
title: Hexo引用站内文章
toc: true
date: 2018-12-16 21:08:02
tags: [Tricks]
categories:
---

在写博客的过程中需要引用自己写的文章, 根据下面的参考资料 1 中所述, 可以使用内置的标签插件的语法 `post_link` 来实现引用, 具体的语法是:

```bash
{% post_link 文章文件名(不要后缀) 文章标题(可选) %}
# {% post_link slug [title] %}
```
其中 `slug ` 就是 `_posts` 文件夹下需要引用的文章的 markdown 文件的名字，title 可以指定引用的文章需要显示的名字.

举个例子, 我的 Wiki 中 `程序语言->Python->实用程序->查找相似图片` 这篇文章中, 需要引用我的另外两篇文章, 相关写法如下:

```bash
+ {% post_link 程序语言/Python/实用程序/Matplotlib响应按键浏览图片 %}
+ {% post_link 程序语言/Python/实用程序/Logger用于log记录 %}
```

因为这两篇文章都在 `source/_posts/程序语言/Python/实用程序/` 目录下.


## 参考资料
> - [Hexo博客搭建之引用站内文章](https://yanyinhong.github.io/2017/05/03/Refer-article-in-hexo-post/)
> - [Hexo引用站内文章](http://www.jibing57.com/2017/10/30/how-to-use-post-link-on-hexo/)
