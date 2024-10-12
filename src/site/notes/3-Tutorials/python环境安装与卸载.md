---
{"dg-publish":true,"title":"python环境安装与卸载","created":"2024-03-22 14:54","updated":"2024-07-17 10:50","tags":["python"],"dg-path":"Tutorials/python环境安装与卸载.md","permalink":"/Tutorials/python环境安装与卸载/","dgPassFrontmatter":true,"noteIcon":"1"}
---


## Python 安装与卸载

> [!hint]  
> 非常推荐使用 conda 创建 python 的虚拟环境来进行开发！

1. 下载并安装 Miniconda3

	[下载地址](https://docs.conda.io/en/latest/miniconda.html)

2. 创建新环境

	尽量避免将软件包安装到 `base` 环境中，以免污染初始环境。

	```shell
	conda create -n ENV_NAME python=PYTHON_VERSION
	
	# 如果需要创建空环境，可以执行以下命令
	conda create -n ENV_NAME
	```

3. 创建新环境至指定路径 (可选)

	在特殊情况下，可能需要将环境安装到工程文件夹下，以便与其他环境隔离。

	```shell
	conda create --prefix ./envs xxx= xxx=
	conda activate ./envs
	```

4. 卸载虚拟环境

	```shell
	# 删除整个虚拟环境
	conda remove -n ENV_NAME --all
	
	# 删除环境中的指定包
	conda remove -n ENV_NAME PACKAGE_NAME
	```

5. 其他命令

	```shell
	# 查看当前环境已经安装的包
	conda list
	
	# 查看当前已经安装的虚拟环境
	conda env list
	
	# 更新当前环境的conda
	conda update conda
	
	# 从已有环境拷贝
	conda create -n NEW_ENV_NAME --clone EXIST_ENV_NAME
	
	# 创造离线环境
	conda create -n NEW_ENV_NAME --offline
	```

## Conda 环境导入/导出
1. 查看可用环境

```shell
conda env list
```

2. 进入需要导出的环境

```shell
conda activate ENV_NAME
```

3. 导出环境

```shell
conda env export > ENV.yml
```

4. 导入环境

```shell
conda env create -f ENV.yml
```

## 离线环境
有些时候需要在不方便联网的机器上安装虚拟环境，可以通过离线的方式安装。

### 通过 Wheel 包安装
1. 在一台可以联网的机器上切换到想要导出的环境，输出环境依赖包到本地文件

	```shell
	pip freeze > requirements.txt
	```

2. 使用 `wheel` 工具将离线包下载到本地文件夹 `wheelhouse`

	```shell
	pip wheel -w wheelhouse -r requirements.txt
	
	# 也可以下载指定离线包
	pip wheel -w wheelhouse PACKAGE1 PACKAGE2 ...
	```

3. 将依赖包文件和离线包文件夹拷贝到其他机器并安装

	```shell
	pip install --no-index --find-links=wheelhouse -r requirements.txt
	```

### 打包整个虚拟环境
1. 在本机安装 conda-pack

	```
	pip install conda-pack
	```

2. 打包 conda 环境

	```
	conda pack -n ENV_NAME [-o out_name.tar.gz]
	```

	打包之后的文件名为 `ENV_NAME.tar.gz`。

3. 上传 `ENV_NAME.tar.gz` 到目标主机的 conda 环境中

	在目标主机的 `path/conda/env` 创建文件夹，再将压缩包解压到文件夹。

4. 最后激活使用

> [!attention]  
> 如果打包过程提示 `CondaPackError: Cannot pack an environment with editable packages`，应在打包指令中添加额外参数 `--ignore-editable-packages`，并在激活环境后运行命令 `python setup.py develop` 或者 `pip install -e`。

## 更换国内镜像源
由于众所周知的原因，我们可能无法直接从官方源下载包，因此可以手动替换镜像源。

1. 更换 pip 源

	```shell
	pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
	pip config set install.trusted-host pypi.tuna.tsinghua.edu.cn
	```

	也可以修改配置文件:

	```shell
	$ vim ~/.pip/pip.conf
	# 将下面内容复制到上面的文件内
	[global]
	index-url = https://pypi.tuna.tsinghua.edu.cn/simple
	[install]
	trusted-host=pypi.tuna.tsinghua.edu.cn
	```

2. 更换 conda 源

	```shell
	$ vim ~/.condarc
	
	channels:
	  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
	  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
	  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
	  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
	ssl_verify: true
	```

## Windows Terminal 使用 Conda

1. 在 windows 上安装 conda 并不能直接在终端上使用，需要在 `系统变量` 添加 conda 路径 `CONDA_PATH/Scripts`。

2. 如果提示 `WindowsPowerShell在此系统上禁止运行脚本`，可以打开 powershell 终端，输入命令: `Get-ExecutionPolicy -List`。

3. 此时 User 的权限是 Undefined，将该权限进行修改：`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`。

4. 重启 powershell 验证问题是否被解决。

