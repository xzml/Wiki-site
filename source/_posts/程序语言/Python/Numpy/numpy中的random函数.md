---
title: numpy中的random函数
toc: true
date: 2018-11-25 19:41:45
tags: [Python, Numpy]
categories:
---

## Random Data

### np.random.rand

```python
np.random.rand(d0, d1, ..., dn)
```

rand 函数根据给定的维度产生 $[0, 1)$ 之间的随机数, 服从均匀分布 (uniform distribution)

### np.random.randn

```python
np.random.randn(d0, d1, ..., dn)
```

randn 函数返回服从标准正态分布 ($\mathcal{N}(0, 1)$) 的随机数, 要返回服从 $\mathcal{N}(\mu, \sigma)$ 的样本, 使用 $\mu * \text{np.random.randn(...)} + \sigma$.

### np.random.randint

```python
np.random.randint(low[, high, size, type])
```

返回 $[low, high)$ 范围内的随机整数, 当 `high` 没有填写时, 默认产生 $[0, low)$ 范围内的随机整数. 默认类型为 `np.int`.

### np.random.random_integers

```python
np.random.random_integers(low[, high, size])
```

返回 $[low, high]$ 范围内的随机整数, 当 `high` 没有填写时, 默认生成随机数的范围为 $[1, low]$. 该函数在新版本的 numpy 中已被替代, 建议使用 `randint` 函数.

### 生成 [0, 1) 区间的浮点数

```python
np.random.random(size=None)
np.random.sample(size=None)
np.random.ranf(size=None)
np.random.random_sample(size=None)
```

以上四个函数都是返回 $[0.0, 1.0)$ 范围内的随机浮点数. 如果要产生服从 $\text{Unif}(b, a), b > a$ 分布的浮点数, 可以使用 $(b - a) * \text{random_sample()} + a$.

### np.random.choice

```python
np.random.choice(a, size=None, replace=True, p=None)
```

从给定的 **一维** 数组中随机选取一个样本

* 参数 `a` 是整数时, 相当于 `np.arange(a)`.
* `size` 为返回数组的大小, 用 tuple 表示.
* 参数 `p` 表示数组中数据出现的概率, 数组之和应该为 1, 并且大小要和 `a` 一样大.
* `replace=False` 时, 生成的随机数不能有重复的数值, 这是一个测试


例如:

```python
>>> import numpy as np
>>> np.random.choice(5, size=(3,), replace=False)
array([1, 2, 0])
>>> np.random.choice(5, size=(6,), replace=False)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "mtrand.pyx", line 1166, in mtrand.RandomState.choice
ValueError: Cannot take a larger sample than population when 'replace=False'
```

## Permutations

### np.random.permutation

```python
np.random.permutation(x)
```

对序列进行随机的非原地排列 (即不是 in-place 的, 会对序列进行拷贝). 如果序列是多维数组, 那么只会沿着第一个 index 进行随机排列.

+ 参数 `x` 如果是个整数, 那么相当于对 `np.arange(x)` 进行随机排列.
+ 考虑多维数组的情况, 见下面的例子:

```python
>>> a = np.arange(9).reshape(3, 3)
>>> np.random.permutation(a)
array([[3, 4, 5],
       [0, 1, 2],
       [6, 7, 8]])
```

## np.random.shuffle

```python
np.random.shuffle()
```

对数组/list 进行原地 shuffle. 注意这个函数直接修改数组, 返回 `None`. 对于多维数组, 只会沿着第一个 index 进行 shuffle.

```python
>>> a
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
>>> np.random.shuffle(a)
>>> a
array([[3, 4, 5],
       [6, 7, 8],
       [0, 1, 2]])
```

## Distributions

### np.random.normal

```python
np.random.normal(loc=0.0, scale=1.0, size=None)
```

对正态 (高斯) 分布进行采样.

$$p ( x ) = \frac { 1 } { \sqrt { 2 \pi \sigma ^ { 2 } } } e ^ { - \frac { ( x - \mu ) ^ { 2 } } { 2 \sigma ^ { 2 } } }$$

其中 $\mu$ 称为 mean, 而 $\sigma$ 称为 standard deviation, $\sigma^2$ 被称为 variance.

```python
>>> mu, sigma = 0, 0.1 # mean and standard deviation
>>> s = np.random.normal(mu, sigma, 1000)
>>> abs(mu - np.mean(s)) < 0.01
True
>>> abs(sigma - np.std(s, ddof=1)) < 0.01
True
```

### np.random.uniform

```python
np.random.uniform(low=0.0, high=1.0, size=None)
```

对均匀分布进行采样. `low` 和 `size` 是可选的.


## Random Generator

### np.random.seed

```python
np.random.seed(seed=None)
```

设置随机种子, 使结果可重复.



## 参考资料
> - [为什么你用不好Numpy的random函数?](https://www.jianshu.com/p/214798dd8f93)
> - [Random sampling (numpy.random)](https://docs.scipy.org/doc/numpy-1.15.1/reference/routines.random.html)
