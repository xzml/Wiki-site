---
title: Ubuntu软件安装
toc: true
date: 2018-11-27 14:36:45
tags: [Ubuntu]
categories:
---

## systemctl: command not found

参考: [systemctl: command not found on ubuntu 16.04](https://askubuntu.com/questions/988266/systemctl-command-not-found-on-ubuntu-16-04)

```bash
sudo apt-get install systemd

# 如果软件损坏, 重装的方法
sudo apt-get reinstall systemd
```


## frpc 设置开机自启动

参考: [Github: frp怎样开机启动和后台运行?](https://github.com/fatedier/frp/issues/176)

1. 在 `/etc/systemd/system/` 中添加 `frpc.service` 文件, 内容如下:

```bash
[Unit]
Description=frpc daemon
After=syslog.target  network.target
Wants=network.target

[Service]
Type=simple
ExecStart=/home/ieric/Programs/frp_0.20.0_linux_amd64/frpc -c /home/ieric/Programs/frp_0.20.0_linux_amd64/frpc.ini
Restart= always
RestartSec=1min
ExecStop=/usr/bin/killall frpc

[Install]
WantedBy=multi-user.target
```

再修改一下 mode, `sudo chmod a+x frpc.service`.

2. 在 `frpc.ini` 配置文件中设置 `login_fail_exit` 的值为 `false` (默认是 `true`), 这样的话, 当启动时没有连上服务器就不会立即退出, 而是每隔 30s 自动重连.

3. 完成以上两步之后, 使用

```bash
sudo systemctl enable frpc
sudo systemctl status frpc

# 这是输出状态
frpc.service - frpc daemon
   Loaded: loaded (/etc/systemd/system/frpc.service; enabled)
   Active: inactive ....
```

## 重启与关机

```bash
sudo reboot -nf
sudo shutdown now
```



## 参考资料
> - []()
> - []()
