---
{"dg-publish":true,"title":"Nginx安装教程","created":"2024-03-04 18:53","updated":"2024-03-04 18:53","tags":["nginx","linux","macos","docker"],"permalink":"/Engineering Wiki/Nginx安装教程/","dgPassFrontmatter":true,"noteIcon":"1"}
---


# 以下是在不同平台上安装 Nginx 的指南

## Debian/Ubuntu

### 步骤 1.更新系统

在开始安装前，建议先更新系统。请运行以下命令:

```shell
sudo apt update && sudo apt upgrade
```

### 步骤 2.安装 Nginx

运行以下命令安装 Nginx:

```shell
sudo apt install nginx
```

### 步骤 3.启动 Nginx

安装 Nginx 后，它应该自动启动。如果没有，请运行以下命令：

```shell
sudo systemctl start nginx
```

### 步骤 4.检查 Nginx 状态

为了确保 Nginx 已成功启动，请运行以下命令：

```shell
sudo systemctl status nginx
```

### 步骤 5.配置防火墙

为了允许访问 Nginx，需要打开端口 80 和/或 443。请运行以下命令：

```shell
sudo ufw allow 'Nginx HTTP'
sudo ufw allow 'Nginx HTTPS'
```

### 步骤 6.检查 Nginx

现在，您可以通过打开 Web 浏览器并在地址栏中输入服务器的 IP 地址来检查 Nginx 是否工作。您应该会看到默认的 Nginx 欢迎页面。

## MacOS

在 Mac 系统中安装 Nginx 需要借助 Homebrew 工具。

### 步骤 1.安装 Homebrew

详情请参考文章 [[Engineering Wiki/Homebrew使用教程\|Homebrew使用教程]]。

### 步骤 2.brew 安装 Nginx

使用 brew 安装 Nginx，请运行以下命令:

```zsh
brew install nginx
```

### 步骤 3.查看 Nginx 配置

查看 nginx 命令，请运行以下命令:

```zsh
brew info nginx
```

Intel 机型的 nginx 安装路径在 `/usr/local/Cellar`，Apple silicon 机型的则在 `/opt/homebrew/Cellar`。

### 步骤 4.启动 Nginx

启动 Nginx 服务，请运行以下命令:

```zsh
brew services start nginx
# 重启
brew services restart nginx
```

### 步骤 5.检查 Nginx

brew 安装的 nginx 默认的端口号是 8080，因此只需要访问 `http://lcoalhost:8080即可`。

## Docker

### 步骤 1.拉取 Nginx 镜像

拉取最新的 Nginx 镜像，请运行以下命令:

```shell
docker pull nginx:latest
```

### 步骤 2.运行容器

使用以下命令来运行 nginx 容器:

```shell
docker run --name some-nginx -p 8080:80 -d nginx
```

可以通过挂载自己的配置文件来实现自定义配置

```shell
docker run --rm --entrypoint=cat nginx /etc/nginx/nginx.conf > /host/path/nginx.conf
```