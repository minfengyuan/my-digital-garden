---
{"dg-publish":true,"title":"WSL2开发环境","created":"2024-01-16 15:17","updated":"2024-01-29 15:00","tags":["ubuntu","cuda"],"permalink":"/Software Developments/WSL2开发环境/","dgPassFrontmatter":true,"noteIcon":"1"}
---


本文记录了笔者在 WSL2 中配置深度学习开发环境的主要步骤。

# 安装 Linux 分支

可以参考 [官方文档步骤6](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-6---install-your-linux-distribution-of-choice)，从 Microsoft Store 安装或手动下载发行版。

# 初始化 WSL2 环境

具体实现过程参考 [[Engineering Wiki/Ubuntu系统安装和初始化\|Ubuntu系统安装和初始化]] 部分。

# 安装远程桌面环境

具体实现过程参考 [[Engineering Wiki/Linux实现远程桌面环境\|Linux实现远程桌面环境]]。

# 配置当前用户执行 Sudo 命令免密

> 由于启动 xrdp 服务需要用到 sudo 权限，会出现输入密码的提示，较为麻烦

1. 创建配置文件

  ```bash
  sudo vim /etc/sudoers.d/[USERNAME]
  ```

2. 编辑如下内容

  ```
  [USERNAME] ALL=(ALL) NOPASSWORD: ALL
  ```

# 创建一键启动脚本

```bash
mkdir ~/.local/bin -p
vim ~/.local/bin/remote.sh
```

文件末尾追加下面两行内容

```
sudo /etc/init.d/xrdp start
sleep 1
mstsc.exe /v:localhost:[PORT]
```

给脚本加上可执行权限

```bash
chmod +x ~/.local/bin/remote.sh
```

在 windows 桌面新建一个快捷方式，对象位置内容填写:

```
wsl ~/.local/bin/remote.sh
```

双击快捷方式即可实现一键启动登录 wsl2 远程桌面

# CUDA on WSL2

还没有开始研究过，可参考如下文章:

1. [CUDA on WSL User Guide](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#getting-started-with-cuda-on-wsl)

2. [Enabling GPU acceleration on Ubuntu on WSL2 with the NVIDIA CUDA Platform](https://ubuntu.com/tutorials/enabling-gpu-acceleration-on-ubuntu-on-wsl2-with-the-nvidia-cuda-platform#3-install-nvidia-cuda-on-ubuntu)