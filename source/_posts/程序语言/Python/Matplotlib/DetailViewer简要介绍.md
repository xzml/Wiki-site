---
title: DetailViewer简要介绍
toc: true
date: 2018-11-27 14:56:46
tags: [Python, Matplotlib]
categories:
---

## DetailViewer 简要介绍

DetaiViewer 是用来放大图像细节的工具, 它意在帮助从事底层图像处理的科研人员方便对比各种算法的效果.

比如在图像去噪, 图像超分辨率的论文中, 我们常常需要将自己的算法和其他的 state-of-the-art 的算法进行视觉效果上的比较, DetailViewer 就是为了方便这一过程, 效果如下图:

![2018-11-14 23.32.10.gif](https://i.loli.net/2018/11/27/5bfced288a415.gif)

下面介绍使用 Python 和 Matplotlib 实现 DetailViewer 的过程.

DetailViewer 包含两个主要的 API:

* `FigureInfo` class
* `RectangleSelection` class

其中最为重要的是 `RectangleSelection` 这个类了, 你可以直接将一个 `matplotlib.figure.Figure` 对象传入到这个类中, 然后就可以利用其中的画出正方形以及移动正方形并动态显示图像细节等功能.

而 `FigureInfo` 只是根据你传入的图像大小和数量简单设置这些图像的布局, 这部分内容其实你自己也可以写. 下面首先重点说一下 `RectangleSelection` 的实现.

在详细说明细节之前, 先介绍一下我的编程环境:

```bash
Anaconda Python 3.5.6
Matplotlib 3.0.0 # 注意 Matplotlib 的版本至少是 3.0.0, 因为会用到 matplotlib.axes.Axes 对象的 inset_axes 方法.
Numpy 1.14.2
PIL (pillow) 5.1.0
scikit-image 0.14.0
```

## 面向对象 API

首先需要认识 Matplotlib 中的 Artist, 关于这一点, 可以详细学习 [Matplotlib Artist Tutorial](https://matplotlib.org/users/artists.html), Artists 主要有两种类型: primitives 和 containers. 像 `Line2D`, `Rectangle`, `Text`, `AxesImage` 等都属于 primitives, 而 `Axes` 和 `Subplots` 就属于 containers. 明确 Artists 的概念后, 再来理解一下 `Axes` 和 `Subplots` 的关系, 比如对于如下代码:

```python
fig, ax = plt.subplots()
```

它返回两个对象: `matplotlib.figure.Figure` 以及 `matplotlib.axes.Axes`, 上面那句代码相当于如下:

```python
fig = plt.figure()
ax = fig.add_subplot(111)

# 和下面的代码还是有一些差异的, 但是本质上是类似的
ax = fig.add_axes([0., 0., 1., 1.])
```

另外需要注意的是 `plt.subplots()` 会返回一个 `Axes` 对象或者一个 Numpy 数组, 比如:

```python
>>> fig, ax = plt.subplots()
>>> print(ax)
AxesSubplot(0.125,0.11;0.775x0.77)

>>> fig, axes = plt.subplots(2, 2)
>>> print(repr(axes))
array([[<matplotlib.axes._subplots.AxesSubplot object at 0x11537a160>,
        <matplotlib.axes._subplots.AxesSubplot object at 0x115392748>],
       [<matplotlib.axes._subplots.AxesSubplot object at 0x1153a9cc0>,
        <matplotlib.axes._subplots.AxesSubplot object at 0x11744b278>]],
      dtype=object)
>>> print(axes.shape)
(2, 2)

# 由于这里 axes 的 shape 是 (2, 2), 那么之后为了用 for 循环方便访问每个 ax, 可以使用:
# for ax in axes.ravel() (或者使用 axes.flat)
```

所以在代码中, 为了处理的统一性, 我会对 `ax` 使用 `ax = np.array(ax)` 进行转换, 方便处理, 防止用户只传入一张图片.


## Matplotlib 中的事件处理和 picking

Matplotlib 提供如下事件处理, 更为详细的信息请查看: [https://matplotlib.org/users/event_handling.html](https://matplotlib.org/users/event_handling.html)

![matplotlib_event.png](https://i.loli.net/2018/11/27/5bfcf5bb09545.png)

在 DetailViewer 中的 `RectangleSeletion` 类中, 事件处理使用 `connect` 方法完成:

```python
def connect(self):
    self.cidpress = self.fig.canvas.mpl_connect(
        'button_press_event', self.on_press)
    self.cidmotion = self.fig.canvas.mpl_connect(
        'motion_notify_event', self.on_motion)
    self.cidpick = self.fig.canvas.mpl_connect(
        'pick_event', self.on_pick)
    self.cidrelease = self.fig.canvas.mpl_connect(
        'button_release_event', self.on_release)
    self.cidkey = self.fig.canvas.mpl_connect(
        'key_press_event', self.on_keypress)
```

从上至下依次是:

* 鼠标按下事件: 确认鼠标的位置, 判断鼠标是否在某个 Axes 中; 只允许画一个矩形;
* 鼠标移动事件: 确认鼠标位置; 确认用户有画矩形的意图; 用户画出矩形; 用户移动矩形的处理方式;
* 矩形移动事件: 用户如果移动矩形, 各种状态的变化;
* 鼠标释放事件: 各种状态的变化; 如果用户画出了矩形, 那么要放大图像;
* 按键响应事件: 针对用户按下键盘上的按键, 做出相应的响应.

每个事件要跟一个操作进行联系, 需要使用 `FigureCanvasBase.mpl_connect` 方法, 比如 `self.fig.canvas.mpl_connect` 方法, 该方法返回一个事件 connection id, 简称 cid, 之后可以用于 `mpl_disconnect` 方法.

下面依次介绍各个事件.

### 状态变量

```python
class RectangleSelection(object):
    def __init__(self, fig):
        self.fig = fig # 画板
        self.cur_axes = None # 鼠标当前所在的 axes
        self.rect = None # 用户画出的矩形
        self.x0, self.y0 = None, None # 矩形的左上角坐标
        self.x1, self.y1 = None, None # 矩形的右下角坐标
        self.is_picking = False # 用户是否选中了矩形
        self.sub_axes = list() # 保存图像上所有的轴域
        self.connect() # 将事件与 handling 进行关联
```

### 鼠标按下事件:

```python

```




## 参考资料
> - [Matplotlib User's Guide: Interactive plots](https://matplotlib.org/users/event_handling.html)
> - []()
