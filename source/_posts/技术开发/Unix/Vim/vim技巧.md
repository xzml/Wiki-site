---
title: vim技巧
toc: true
date: 2018-11-26 23:11:51
tags: [Unix, Vim]
categories:
---

## 调整窗口的高度和宽度

参考博客 1. 其中使用 `c-w, >` 和 `c-w, <` 最为方便.

```bash
CTRL-W =        使得所有窗口 (几乎) 等宽、等高，但当前窗口使用 'winheight' 和 'winwidth'。

:res[ize] -N                               
CTRL-W -        使得当前窗口高度减 N (默认值是 1)。如果在 'vertical' 之后使用，则使得宽度减 N。

:res[ize] +N                                    
CTRL-W +        使得当前窗口高度加 N (默认值是 1)。如果在 'vertical' 之后使用，则使得宽度加 N。

:res[ize] [N]
CTRL-W CTRL-_                                  
CTRL-W _        设置当前窗口的高度为 N (默认值为最大可能高度)。

:vertical res[ize] [N]                  
CTRL-W |        设置当前窗口的宽度为 N (默认值为最大可能宽度)。

z{nr}<CR>       设置当前窗口的高度为 {nr}。
                                           
CTRL-W <        使得当前窗口宽度减 N (默认值是 1)。                                              
CTRL-W >        使得当前窗口宽度加 N (默认值是 1)。
```




## 参考资料
> - [vim: vs sp 调整窗口高度和宽度](https://www.cnblogs.com/xuechao/archive/2011/03/29/1999292.html)
> - []()
