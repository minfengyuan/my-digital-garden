---
{"dg-publish":true,"title":"Ubuntu常用命令","created":"2024/01/16, 15:09","updated":"2024/01/16, 15:17","tags":["ubuntu"],"permalink":"/笔记/Ubuntu常用命令/","dgPassFrontmatter":true}
---


本文记录笔者在日常使用 Ubuntu 时常见的命令。

# 文件权限管理

## 修改文件所属组群—chgrp

```bash
chgrp $group /path/to/change/group
chgrp -R $group /path/to/change/group # 改变同一目录下的所有文件夹
```

## 修改文件所有者—chown

```bash
chown $user /path/to/change/group
chown -R $user /path/to/change/group # 改变同一目录下的所有文件夹
```

## 文件权限—chmod

[Linux chmod 命令 | 菜鸟教程](https://www.runoob.com/linux/linux-comm-chmod.html)

权限位共有 10 位：第一位是文件类型，第二到四位是所有者，第五到七位是所属组，第八到十位是其他人。

# 自启动

## 通过 rc.local 服务

1. 将 /lib/systemd/system/rc-local.service 链接到 /etc/systemd/system/ 目录下面来

  ```bash
  ln -fs /lib/systemd/system/rc-local.service /etc/systemd/system/rc-local.service
  ```

2. 修改 rc-local.service 文件内容

  ```bash
  sudo echo "
  [Install]
  WantedBy=multi-user.target
  Alias=rc-local.service
  " >> etc/systemd/system/rc-local.service
  ```

3. 创建并编辑/etc/rc.local 文件

  ```bash
  sudo touch /etc/rc.local
  sudo vim /etc/rc.local
  ```

4. 把想开机启动的脚本放入

  ```bash
  #!/bin/bash
  [此处为脚本]
  ```

## 通过 init.d 脚本文件

1. 编写脚本

  ```bash
  vim test.sh
  ```

	先添加脚本内容抬头

    ```bash
    #!/bin/bash
    
    ### BEGIN INIT INFO
    # Provides:     test
    # Required-Start:  $remote_fs $syslog
    # Required-Stop:   $remote_fs $syslog
    # Default-Start:   2 3 4 5
    # Default-Stop:   0 1 6
    # Short-Description: start test
    # Description:    start test
    ### END INIT INFO
    ```
	然后开始编写脚本内容

2. 将编写的脚本放入 `/etc/init.d/`

    ```bash
    sudo mv test.sh /etc/init.d/
    ```

	给文件权限

    ```bash
    chmod +750 test.sh
    ```

	设置开机自动启动
 
    ```bash
    sudo update-rc.d test.sh defaults NN
    ```

	NN为启动顺序，如果需要用到网络，则设置一个较大的数字如99.

3. 卸载启动脚本

    ```bash
    cd /etc/init.d
    sudo update-rc.d -f test.sh remove
    ```

# 移除软件

  ```bash
  sudo apt-get --purge remove 软件名
  ```

# 磁盘分区

fdisk 命令详解：

m：获取帮助

n：新建分区

p：显示分区表

d：删除分区

b：设置卷标

w：写入分区表

t：改变分区文件系统类型

v：检验分区

l：显示 fdisk 所支持的文件系统代码

q：退出

### 格式化分区

```bash
mkfs -t ext4 /dev/sda1
mkfs -t ext4 /dev/sda2
```

# 后台进程管理

1. &  
加在一个命令的最后，可以把这个命令放到后台执行 ,如 gftp &,  
2. ctrl + z  
可以将一个正在前台执行的命令放到后台，并且处于暂停状态，不可执行  
3. jobs  
查看当前有多少在后台运行的命令  
jobs -l 选项可显示所有任务的 PID，jobs 的状态可以是 running, stopped, Terminated,但是如果任务被终止了（kill），shell 从当前的 shell 环境已知的列表中删除任务的进程标识；也就是说，jobs 命令显示的是当前 shell 环境中所起的后台正在运行或者被挂起的任务信息；  
4. fg  
将后台中的命令调至前台继续运行  
如果后台中有多个命令，可以用 fg %jobnumber 将选中的命令调出，%jobnumber 是通过 jobs 命令查到的后台正在执行的命令的序号 (不是 pid)  
5. bg  
将一个在后台暂停的命令，变成继续执行 （在后台执行）  
如果后台中有多个命令，可以用 bg %jobnumber 将选中的命令调出，%jobnumber 是通过 jobs 命令查到的后台正在执行的命令的序号 (不是 pid)  
将任务转移到后台运行：  
先 ctrl + z；再 bg，这样进程就被移到后台运行，终端还能继续接受命令。

## ctrl+z（挂起）、ctrl+c（中断）、ctrl+\（退出）和 ctrl+d（EOF）的区别

ctrl+c 强行中断当前程序的执行。

ctrl+z 将任务中断,但是此任务并没有结束,他仍然在进程中，只是放到后台并维持挂起的状态。如需其在后台继续运行，需用“bg 进程号”使其继续运行；再用 "fg 进程号 " 可将后台进程前台化。

ctrl+\表示退出。

ctrl+d 表示结束当前输入（即用户不再给当前程序发出指令），那么 Linux 通常将结束当前程序。

# 清理空间

## To Server

在服务器上使用 ncdu

```bash
sudo apt-get install ncdu
# 以清理日志文件为例
sudo ncdu /var/log
```

## To Client

在桌面版本用图形化的 baobab 工具。

```bash
sudo apt-get install baobab
```

# 压缩解压

```bash
# 压缩
tar -zcvf xx.tar.gz <target folder>/
# 解压
tar -zxvf xx.tar.gz <destination folder>
```

# 磁盘操作

## 挂载磁盘

1. 查看硬盘 UUID

    ```bash
    sudo blkid
    ```

2. 创建挂载点

    ```bash
    sudo mkdir /mnt/xxx
    ```

3. 挂载

    ```bash
    mount /dev/xxx /mnt/xxx
    ```

4. 开机自动挂载

    ```bash
    # 修改/etc/fstab文件内容
    sudo vim /etc/fstab
    # 添加到文件
    UUID=XXX-XXX-XXX /mnt/xx ext4 defaults 0 2
    # 挂载NTFS文件系统
    UUID= /mnt/xxx ntfs defaults 0 2
    ```