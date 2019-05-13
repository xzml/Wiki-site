---
title: pytorch-grad
toc: true
date: 2019-05-13 23:12:58
tags: [Python, PyTorch]
categories:
---

## PyTorch 的自动求导

先看一个小例子:

```python
import torch
from torch.autograd import Variable
import torch.nn as nn


x = Variable(torch.ones(1, 2), requires_grad=True)
linear = nn.Linear(2, 2, bias=False)
linear.weight.data.copy_(torch.Tensor([[1, 2], [1, 2]]))

y = x
for i in range(2):
    y = linear(y)

loss = y.sum()
loss.backward()
print(linear.weight.grad)
print(x.grad)
```

其中 $y = W\cdot(W\cdot x)$, 使用 `.backward()` 即可自动求导了.





## 参考资料
> - []()
> - []()
