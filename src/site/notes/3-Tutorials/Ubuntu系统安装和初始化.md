---
{"dg-publish":true,"title":"Ubuntu系统安装和初始化","created":"2024-01-16 15:08","updated":"2024-01-29 15:00","tags":["ubuntu"],"dg-path":"Tutorials/Ubuntu系统安装和初始化.md","permalink":"/Tutorials/Ubuntu系统安装和初始化/","dgPassFrontmatter":true,"noteIcon":"1"}
---


本文简单记录笔者在 Ubuntu 系统安装和初始化时进行的操作。

# 系统安装

1. Through 镜像

    下载 [官方镜像](https://ubuntu.com/download#download)，制作成启动盘

    格式化一块硬盘分区，再按下面的方式划分系统分区 (最简)

    - swap 逻辑分区      电脑内存 1-2 倍（我的是 16GB 内存，所以设置为 16GB）
    - /           逻辑分区或主分区 Ext4 日志文件系统    余下空间
    - /home 可以考虑给用户文件夹单独一个分区

    划分好分区后，需要注意的是挂载点为“/”的那一行，前面设备那一列显示应该是/dev/sdaX,X 是一个阿拉伯数字，具体是几每个人不一样，表示的是你的/分区按在硬盘的第几块位置，记住/这个分区的 sda 后面是什么数字，然后在下面安装启动引导器设备：处，选择 sdaX（X 是一个阿拉伯数字）的那一项，也就是说，要将启动引导器安装在/分区中。</p>

2. Through 虚拟机

	*待补充...*

## Ubuntu 启动引导

1. 编辑 grub 配置文件：`sudo vim /etc/default/grub`

2. 注释掉：`GRUB_TIMEOUT_STYLE=hidden`

3. 修改 grub 等待时间，单位是秒：`GRUB_TIMEOUT=10`

4. 编辑：`GRUB_CMDLINE_LINUX_DEFAULT=”text”`

5. 修改默认启动项

	```
	GRUB_DEFAULT = saved  # 上一次启动的选择项
	GRUB_DEFAULT= 2  # 这个是选择第三项：Windows
	```

6. 执行 `sudo update-grub` 命令应用修改

## Windows、Ubuntu 双系统时差

终端执行命令

```bash
timedatectl set-local-rtc 1 --adjust-system-clock
```

## Issues

### Wifi 无法连接解决方案

搜索不到附近的 wifi 热点，原因是 ubuntu 默认关闭了联想 Y7000 的 wifi 硬件，即硬阻塞显示无线和蓝牙状态，可查看状态：

`rfkill list all`

打开文件：

`sudo gedit /etc/modprobe.d/blacklist.conf`

最后一行加入：

`blacklist ideapad_laptop`

最后保存重启

# 新建用户并添加 Sudo 权限

1. 如果没有 root 用户
  
    ```bash
    sudo passwd root
    ```

2. 新建用户

    ```bash
    # 新建账号并自动建立用户目录
    sudo useradd -r -m -s /bin/bash username
    # 为用户username添加sudo权限
    sudo usermod -a -G sudo username
    # 去除用户username的sudo权限
    sudo usermod -G usergroup username
    ```

# 时间同步

1. 使用 `date` 或 `timedatectl` 确认当前时间

2. 如果看到 `System clock synchronized` 值为 `no`，则重启该服务

    ```bash
    sudo systemctl restart systemd-timesyncd.service
    ```

    如果仍然不起作用，请运行以下命令以启用时间同步：

    ```bash
    sudo timedatectl set-ntp true
    ```
 
3. 如果非 CST（中国标准时间），则更改时区

    ```bash
    sudo timedatectl set-timezone Asia/Shanghai
    ```

# Ca-certificate 失效

手动下载 [最新的证书文件](http://archive.ubuntu.com/ubuntu/pool/main/c/ca-certificates/) 重新安装

```bash
cd /tmp
wget http://archive.ubuntu.com/ubuntu/pool/main/c/ca-certificates/ca-certificates_20230311ubuntu0.20.04.1_all.deb
sudo dpkg -i ./ca-certificates_20230311ubuntu0.20.04.1_all.deb
```

# 替换 Apt 源

1. 使用 `cat /etc/os-release` 或 `lsb_release -c` 命令确认当前 ubuntu 系统代号 Codename

2. 备份软件源配置文件

    ```bash
    sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

3. 拷贝，注意替换系统代号

	- [清华大学开源镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)
	- [中科大开源镜像站](https://mirrors.ustc.edu.cn/help/ubuntu.html#id7)

    ```bash
    # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    
    # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    # # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    
    deb http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
    # deb-src http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
    
    # 预发布软件源，不建议启用
    # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
    # # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
    ```

4. 更新

    ```bash
    sudo apt update
    sudo apt upgrade -y --fix-missing
    sudo apt install -y --fix-missing
    ```

# 字体

1. [consolas](https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/uigroupcode/YaHei.Consolas.1.12.zip)
2. [Fira Code](https://github.com/tonsky/FiraCode)