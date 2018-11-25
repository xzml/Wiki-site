---
title: 关于Wiki的主题
toc: true
date: 2018-11-25 22:35:41
tags: [Tricks]
categories:
---

## 不可思议的 Tricks

比如使用 Markdown 中的列表, 像如下代码是不会显示列表前面的点 $\bullet$:

```markdown
+ `a` 是一个数组
```

效果如下:

+ `a` 是一个数组

需要写成:

```markdown
+ 参数 `a` 是一个数组.
```

才能得到想要的效果:

+ 参数 `a` 是一个数组.

## 修改 Wikitten 模板的属性

### 修改字体

可以去 `themes/Wikitten/source/css` 目录下, 查看 `style.styl` 文件, 发现其中还会导入 `_variables.styl` 文件, 只需要修改该文件中的相关属性即可.

### 修改代码使用的主题

当前我使用的代码主题 (配色) 是 `solarized-light`, 要查看其它的配色, 可以看 `themes/Wikitten/source/css/_highlight` 目录, 里面有大量的配色文件, 这些文件的文件名就是某种主题, 只需要将文件名填入 `themes/Wikitten/_config.yml` 中的 `highlight` 项目下即可. 比如:

```bash
highlight: solarized-dark # monakai
```


## 参考资料

参考资料中有 Wikitten 主题的地址, 以及原作者写的关于使用 Hexo 搭建个人 Wiki 的博客.

> - [使用 Hexo 做个人 Wiki 知识管理系统](https://www.v2ex.com/t/347176?p=2)
> - [作者的个人 Wiki 地址](https://wiki.zthxxx.me/)
> - [Wikitten 主题地址](https://github.com/zthxxx/hexo-theme-Wikitten)
