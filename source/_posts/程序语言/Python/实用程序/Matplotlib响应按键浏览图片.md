---
title: Matplotlib响应按键浏览图片
toc: true
date: 2018-12-16 19:10:40
tags: [Python, Matplotlib]
categories:
---

最近写了一个查找相似图像的算法, 目标是给定 A 图片, 然后在目录 `B/` 中查找和 A 图片相似的图片. 假设在 `B/` 中我找到了 10 张相似图片, 但是要查看这 10 张图, 在 Mac 上我先到文件夹下, 然后使用 preview 进行查看, 有点麻烦, 我想是否可以用 matplotlib 显示这些图片, 然后按下 `n` 就浏览下一张, 按下 `N` 浏览上一张. 于是就有了下面的脚本, 同时复习一下 Matplotlib 的事件处理代码如何写.

![2018-12-16 19.50.03.gif](https://i.loli.net/2018/12/16/5c163d7c4311f.gif)

## ImageViewer 类的实现

`ImageViewer` 类的具体实现如下, `img_list` 是包含图像绝对路径的 list. 代码最后给了 demo, 很简单. demo 中的效果是浏览一个文件夹中的图片, 按 `n` 访问下一张, 按 `N` 访问上一张.

```python ImageViewer.py
import numpy as np
from PIL import Image
import matplotlib
import matplotlib.pyplot as plt

class ImageViewer(object):
    def __init__(self, img_list):
        self.img_list = list(img_list)
        self.fig, self.ax = plt.subplots()
        self.num_of_img = len(self.img_list)
        self.indicator = 0
        self.connect()

    def path2array(self, img_path):
        im = Image.open(img_path).convert('RGB')
        im = np.array(im)
        return im

    def show(self):
        if self.img_list:
            im = self.path2array(self.img_list[self.indicator])
            self.ax.imshow(im)
            plt.show()

    def connect(self):
        self.cidkey = self.fig.canvas.mpl_connect(
            'key_press_event', self.on_keypress)

    def on_keypress(self, event):
        if self.img_list:
            if event.key in ['n']:
                self.indicator += 1
            elif event.key in ['N']:
                self.indicator -= 1

            # dont worry about negative self.indicator, % will automatically handle it
            self.indicator = self.indicator % self.num_of_img
            im = self.path2array(self.img_list[self.indicator])
            self.ax.imshow(im)

        self.fig.canvas.draw()

if __name__ == '__main__':
    import os
    from os.path import join, exists
    img_dir = './bsd100'
    img_list = [join(img_dir, name) for name in os.listdir(img_dir)]
    viewer = ImageViewer(img_list)
    viewer.show()
```

### 按键响应

Matplotlib 提供 `key_press_event`, 回调函数为 `self.on_keypress`. 在该函数中, 使用 `self.indicator` 记录当前指向第几张图片 (从 0 开始计数).

为了循环访问的效果, 使用求余符号 `%` 控制 `self.indicator` 的变化.

```python
def connect(self):
    self.cidkey = self.fig.canvas.mpl_connect(
        'key_press_event', self.on_keypress)

def on_keypress(self, event):
    if self.img_list:
        if event.key in ['n']:
            self.indicator += 1
        elif event.key in ['N']:
            self.indicator -= 1

        # dont worry about negative self.indicator, % will automatically handle it
        self.indicator = self.indicator % self.num_of_img
        im = self.path2array(self.img_list[self.indicator])
        self.ax.imshow(im)

    self.fig.canvas.draw()
```


## 参考资料
> - []()
> - []()
