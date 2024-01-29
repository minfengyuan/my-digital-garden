---
{"dg-publish":true,"title":"Linux实现远程桌面环境","created":"2024-01-16 15:16","updated":"2024-01-29 15:00","tags":["ubuntu","tool"],"dg-path":"环境部署/Linux实现远程桌面环境.md","permalink":"/环境部署/Linux实现远程桌面环境/","dgPassFrontmatter":true,"noteIcon":""}
---


本文简单记录在 Linux 实现远程连接桌面环境所需安装的依赖。

# 环境安装

1. 安装 xfce4 和 xrdp

  ```bash
  sudo apt-get update -y
  sudo apt-get install -y xfce4 xrdp --fix-missing
  ```

2. 修改默认配置

  ```bash
  $ sudo vim /etc/xrdp/xrdp.ini

  # 修改默认的通信端口3389，防止和windows系统的远程桌面冲突
  port=3390

  # 修改Xorg配置部分以保存登录使用的账号密码，即可实现免密登录
  [Xorg]
  name=Xorg
  lib=libxup.so
  username=[USERNAME]
  password=[PASSWORD]
  ip=127.0.0.1
  port=-1
  code=20
  ```

3. 指定session会话

  ```bash
  echo "xfce4-session" >> ~/.xsession
  ```

# 启用服务

```bash
sudo /etc/init.d/xrdp start
```