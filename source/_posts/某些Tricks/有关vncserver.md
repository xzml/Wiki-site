---
title: 有关vncserver
toc: true
date: 2019-04-27 12:55:14
tags: [Tricks]
categories:
---

## VNCserver

参考资料非常详细了, 这里简要概括一下.

### 安装 vncserver

```bash
sudo apt-get install gnome-core
sudo apt-get install vnc4server
```

使用如下命令在某用户目录开启 vncserver, 并设置密码:

```bash
cd ~/username
vncserver
```

关闭 vncserver, 冒号后面是端口号:

```bash
vncserver -kill :1
```

为了用上 xfce4 桌面, 需要修改 xstartup 文件, 该文件在 `~/.vnc` 目录下, 这个目录需要先运行 `vncserver` 命令才会生成:

```bash
cd ~/username/.vnc
vim xstartup
```

修改成如下形式:

```bash
#!/bin/sh
 
# Uncomment the following two lines for normal desktop:
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
startxfce4 &
# exec /etc/X11/xinit/xinitrc

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &
# x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
# x-window-manager &
```

之后重新开启 vncserver:

```bash
vncserver -geometry 1024x600
```

## 开机启动 vncserver

首先在 `/etc/` 目录下创建一下文件:

```bash
sudo mkdir -p /etc/vncserver
sudo vim /etc/vncserver/vncservers.conf
```

`vncservers.conf` 内容如下:

```bash
VNCSERVERS="1:arbab 2:ieric"
VNCSERVERARGS[1]="-geometry 1024x600 -depth 24"
VNCSERVERARGS[2]="-geometry 1024x600 -depth 24"
```

有新用户加入就按上面的格式增加新的用户名以及设置 vncserver 启动的参数.

下面设置开机启动的脚本, 首先在 `/etc/init.d` 目录下创建名为 `vncserver` 的脚本:

```bash
sudo touch /etc/init.d/vncserver
sudo chmod +x /etc/init.d/vncserver
sudo vim /etc/init.d/vncserver
```

开机启动的脚本来自: [https://superuser.com/questions/147109/automatically-start-vnc-server-on-startup](https://superuser.com/questions/147109/automatically-start-vnc-server-on-startup), 将下面内容写进 `vncserver` 文件中:

```bash
#!/bin/bash
unset VNCSERVERARGS
VNCSERVERS=""
[ -f /etc/vncserver/vncservers.conf ] && . /etc/vncserver/vncservers.conf
prog=$"VNC server"
start() {
 . /lib/lsb/init-functions
 REQ_USER=$2
 echo -n $"Starting $prog: "
 ulimit -S -c 0 >/dev/null 2>&1
 RETVAL=0
 for display in ${VNCSERVERS}
 do
 export USER="${display##*:}"
 if test -z "${REQ_USER}" -o "${REQ_USER}" == ${USER} ; then
 echo -n "${display} "
 unset BASH_ENV ENV
 DISP="${display%%:*}"
 export VNCUSERARGS="${VNCSERVERARGS[${DISP}]}"
 su ${USER} -c "cd ~${USER} && [ -f .vnc/passwd ] && vncserver :${DISP} ${VNCUSERARGS}"
 fi
 done
}
stop() {
 . /lib/lsb/init-functions
 REQ_USER=$2
 echo -n $"Shutting down VNCServer: "
 for display in ${VNCSERVERS}
 do
 export USER="${display##*:}"
 if test -z "${REQ_USER}" -o "${REQ_USER}" == ${USER} ; then
 echo -n "${display} "
 unset BASH_ENV ENV
 export USER="${display##*:}"
 su ${USER} -c "vncserver -kill :${display%%:*}" >/dev/null 2>&1
 fi
 done
 echo -e "\n"
 echo "VNCServer Stopped"
}
case "$1" in
start)
start $@
;;
stop)
stop $@
;;
restart|reload)
stop $@
sleep 3
start $@
;;
condrestart)
if [ -f /var/lock/subsys/vncserver ]; then
stop $@
sleep 3
start $@
fi
;;
status)
status Xvnc
;;
*)
echo $"Usage: $0 {start|stop|restart|condrestart|status}"
exit 1
esac
```

注意: 每个用户都要手动开启 vncserver, 即重启之前, vncserver 在该用户目录下一定要是运行状态.

最后再执行:

```bash
sudo update-rc.d vncserver defaults 99
```

现在重启 vncserver:

```bash
sudo service vncserver restart
```

如果添加新的用户, 那么可以:

```bash
sudo adduser newuser
su - newuser
vncserver # 在 ~/newuser 目录下开启 vncserver
vim ~/.vnc/xstartup # 修改 xstartup 文件, 用上面介绍的 xstartup 内容替换, 使用 xfce4 桌面
sudo vim /etc/vncserver/vncserver.conf # 在配置文件中增加新用户, 用于开机启动
# 如果不想重启所有人的 vncserver, 可以到新用户目录下使用:
cd ~/newuser; vncserver -kill :3; vncserver # 进行重启
# 下面命令会重启所有人的 vncserver
sudo service vncserver restart # 重启 vncserver
```


## 参考资料
> - [How to install VNC server on Ubuntu Server 12.04](https://rbgeek.wordpress.com/2012/06/25/how-to-install-vnc-server-on-ubuntu-server-12-04/)
> - [multiple users on vnc](https://rbgeek.wordpress.com/tag/multiple-users-on-vnc/)
