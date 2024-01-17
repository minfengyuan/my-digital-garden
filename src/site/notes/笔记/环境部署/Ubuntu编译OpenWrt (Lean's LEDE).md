---
{"dg-publish":true,"title":"Ubuntu编译OpenWrt (Lean's LEDE)","created":"2024/01/16, 15:20","updated":"2024/01/16, 15:20","tags":["ubuntu"],"dg-path":"环境部署/Ubuntu编译OpenWrt (Lean's LEDE).md","permalink":"/环境部署/Ubuntu编译OpenWrt (Lean's LEDE)/","dgPassFrontmatter":true,"noteIcon":""}
---


# 基础操作

1. 根据 [[笔记/环境部署/Ubuntu系统安装和初始化\|Ubuntu系统安装和初始化]] 准备好一个能科学的 Ubuntu 机器

2. 安装编译 OpenWrt 所需的依赖

	```bash
	sudo apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential \
	bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
	git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libglib2.0-dev libgmp3-dev libltdl-dev \
	libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libreadline-dev libssl-dev libtool lrzsz \
	mkisofs msmtp nano ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 python3-pyelftools \
	libpython3-dev qemu-utils rsync scons squashfs-tools subversion swig texinfo uglifyjs upx-ucl unzip \
	vim wget xmlto xxd zlib1g-dev python3-setuptools
	```

3. 下载源代码，更新 feeds 并选择配置

	```bash
	git clone https://github.com/coolsnowwolf/lede
	cd lede
	./scripts/feeds update -a
	./scripts/feeds install -a
	make menuconfig
	```

4. 下载 dl 库，编译固件 （-j 后面是线程数，第一次编译推荐用单线程）

	```bash
	make download -j8
	make V=s -j1
	```

5. 二次编译

	```bash
	cd lede
	git pull
	./scripts/feeds update -a
	./scripts/feeds install -a
	make defconfig
	make download -j8
	make V=s -j$(nproc)
	```

> [!success]  
> 编译完成后输出路径：`bin/targets`

# 定制操作

## 修改默认主题

1. 删除 lede 的 luci 源中的 argon 旧版本主题 (需先 feeds update)

	```bash
	rm -rf feeds/luci/themes/luci-theme-argon
	```

2. 拉取最新的 18.06 分支的 argon 主题源码到 `package/downloads/luci-theme-argon`

	```bash
	git clone -b 18.06 https://github.com/jerrykuku/luci-theme-argon.git package/downloads/luci-theme-argon
	```

3. 修改 lede 的默认主题,位置 `feeds/luci/collections/luci/Makefile`

	```bash
	sed -i 's/luci-theme-bootstrap/luci-theme-argon/' feeds/luci/collections/luci/Makefile
	```

4. 确认默认主题是否修改成功

	```bash
	make menuconfig
	```

	选择 `LuCI->Themes->luci-theme-argon`</p>

5. 编译固件

	```bash
	make -j1 V=s
	```