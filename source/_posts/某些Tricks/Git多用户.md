---
title: Git多用户
toc: true
date: 2018-11-25 23:29:18
tags: [Tricks]
categories:
---

## 设置 SSH

使用 `ssh-keygen` 产生新的秘钥, 然后将 `.pub` 放到 Github 上. 之后修改 `.ssh/config` 文件, 设置

```bash
Host xzml
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_xzml
```

最后使用 `ssh -T xzml` 对 Github 进行访问, 如果成功的话, 会返回:

```bash
Hi xzml! You've successfully authenticated, but GitHub does not provide shell access.
```





## 参考资料
> - []()
> - []()
