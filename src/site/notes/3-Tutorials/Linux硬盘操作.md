---
{"dg-publish":true,"title":"Linux硬盘操作","created":"2024-02-26 10:38","updated":"2024-02-26 10:38","tags":["linux"],"dg-path":"Tutorials/Linux硬盘操作.md","permalink":"/Tutorials/Linux硬盘操作/","dgPassFrontmatter":true,"noteIcon":"1"}
---

# 查看当前磁盘信息

```shell
fdisk -l
# or
lsblk -l
```

# 更改分区类型

> [!NOTE] 解释  
> MBR 支持的磁盘最大容量为 2TiB，当挂载的磁盘容量超过 2TiB 时，需要将磁盘分区改为 GPT 格式。GPT 最大支持的磁盘容量为 18EiB。

硬盘分区引导改为 GPT 格式需要借助 `parted` 命令工具。

```shell
sudo apt install parted -y

parted /dev/sdb  # 对磁盘sdb进行操作
mklabel gpt  # 转为GPT分区
print  # 查看当前磁盘的分区表
```

# 分区操作

1. 输入命令 `lsblk` 并按回车，查看目录结构。
2. 为硬盘添加分区，输入命令 `fdisk /dev/sdb` 并按回车，再输入 `m` 获取帮助。  
	常用的几个命令说明:  
> m: 显示帮助菜单  
> n: 新建分区  
> d: 删除分区  
> p: 显示分区列表  
> q: 不保存退出  
> w: 保存退出  
3. 输入 `n` 并按回车，添加一个新的分区，根据提示进行下一步操作。
4. 输入 `w` 并按回车，保存操作并退出。
5. 此时分区已完成。再次输入 `lsblk -f` 并按回车，确认分区正确。

# 格式化分区

1. 输入命令 `mkfs -t ext4 /dev/sdb` 并按回车，将 `dev/sdb` 分区格式化为 ext4 类型。
2. 输入命令 `lsblk -f` 并按回车，查看 `dev/sdb` 是否格式化正确。

# 挂载分区

## 临时挂载

1. 输入命令 `mount 设备名称 挂载目录`（例如 `mount /dev/sdb1 /mnt/sdb1`）并按回车，将 `dev/sdb1` 分区挂载到 `mnt/sdb1` 目录。
2. 输入命令 `df -Th` 并按回车，查看 Linux 挂载分区是否成功。

> [!caution] 注意  
> 这种挂载属于临时挂载，重启系统就失效了。

## 永久挂载

1. 输入命令 `blkid /dev/sdb1` 并按回车，查看 `dev/sdb1` 的 UUID，复制 UUID 信息。
2. 输入命令 `vi /etc/fstab` 并按回车编辑文件，输入以下内容 `UUID/磁盘路径 挂载点 磁盘类型 defaults 0 0`。
3. 输入命令 `mount -a` 并按回车。检查挂载点信息是否正确，正确不会打印信息。
4. 重启电脑后，输入命令 `df -h` 并按回车，检查挂载目录是否正确。