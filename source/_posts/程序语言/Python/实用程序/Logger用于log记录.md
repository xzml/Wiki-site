---
title: Logger用于log记录
toc: true
date: 2018-12-16 19:11:57
tags: [Python]
categories:
---

写程序进行日志记录真的非常方便, 这里提供 `InfoLogger` 类, 用于对日志的记录. 比如:

```python
logger = InfoLogger(opt=None)
logger.info('Hello World')
```

默认创建 `log/` 文件夹并在该文件夹中记录日志, 如果要修改目录, 那么就要传入 `opt` 对象, 该对象需要分别指定 `opt.log_checkpoint` 属性说明日志存放的目录, 以及 `opt.log_file` 属性: log 文件的名字.

另外还提供 `args_namespace` 函数, 用于处理 `argparse.Namespace` 对象 (即 `opt = parser.parse_args()`). 具体效果看下面的内容.

关于 opt 对象, 有 `log_checkpoint` 或 `log_file` 就行, 没有这两个属性就采用默认值.

```python
parser = argparse.ArgumentParser('Logger')
parser.add_argument('--log_checkpoint', type=str, default='log/', help='log directory')
opt = parser.parse_args()

# or
opt = type('Option', (object,), {})
opt.log_checkpoint = 'log/'
```

## InfoLogger 类的实现

```python Logger.py
import logging
from datetime import datetime
from pprint import pprint as pp
from os.path import exists, join
import os

def args_namespace(opt):
    res = ["{}: {}\n".format(attr, getattr(opt, attr)) for attr in vars(opt)]
    return "Argument Settings:\n" + \
            "===============================================================\n" + \
            "".join(res) + \
            "==============================================================="

class InfoLogger(object):
    def __init__(self, opt=None):
        super(InfoLogger, self).__init__()
        if not getattr(opt, 'log_checkpoint', None):
            log_checkpoint = 'log'
        else:
            log_checkpoint = opt.log_checkpoint

        if not getattr(opt, 'log_file', None):
            log_file = "{:%Y-%m-%d-%H:%M:%S}.log".format(datetime.now())
        else:
            log_file = opt.log_file

        if not exists(log_checkpoint):
            os.makedirs(log_checkpoint)

        logging.basicConfig(level=logging.INFO)
        self.logger = logging.getLogger('main')
        handler = logging.FileHandler(join(log_checkpoint, log_file), mode='w')
        handler.setLevel(logging.INFO)
        formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s', 
                                      datefmt='%Y-%m-%d %H:%M:%S')
        handler.setFormatter(formatter)
        self.logger.addHandler(handler)

    def __getattr__(self, item):
        return self.logger.__getattribute__(item)


if __name__ == '__main__':
    # you can also use argparse, I use `type` here just for convenience.
    opt = type('Option', (object, ), {})
    opt.log_checkpoint = './infologger'
    opt.log_file = 'test.log'

    logger = InfoLogger(opt)
    logger.info(args_namespace(opt))
    logger.info('hello world')
```

demo 效果如下:

![20181216-log.png](https://i.loli.net/2018/12/16/5c1640ab4af4f.png)


## 参考资料
> - []()
> - []()
