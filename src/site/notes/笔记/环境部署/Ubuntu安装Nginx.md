---
{"dg-publish":true,"title":"Ubuntu安装Nginx","created":"2024-01-16 15:17","updated":"2024-01-29 15:00","tags":["ubuntu","nginx","tool"],"dg-path":"环境部署/Ubuntu安装Nginx.md","permalink":"/环境部署/Ubuntu安装Nginx/","dgPassFrontmatter":true,"noteIcon":""}
---


Nginx 是开源的高性能 HTTP 和反向代理服务器，负责处理 Internet 上一些最大站点的负载。本文简单介绍了如何安装 Nginx 服务，以及如何配置反向代理、SSL 加密等。

# 安装 Nginx

Nginx 在默认的 Ubuntu 存储库中可用。要安装它，请使用 `apt` 命令运行以下命令，这将会更新软件包索引并且安装 Nginx 。

安装完成后，Nginx 服务将自动启动。您可以通过运行 `systemctl` 命令查看服务的状态，从而确定 Nginx 服务是否正常启动。

```bash
sudo apt update
sudo apt install nginx
sudo systemctl status nginx
```

# 配置反向代理

## 步骤 1：安装 Nginx

确保已在服务器上安装了 Nginx。

## 步骤 2：编辑 Nginx 配置文件

1. 打开 Nginx 的配置文件，通常位于 `/etc/nginx/nginx.conf` 或 `/etc/nginx/sites-available/default`。

2. 在配置文件中找到或添加一个 `server` 段落，根据需要进行配置。示例：

    ```
    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://your_upstream_server;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            # 其他可能需要的配置项
        }
    }
    ```

    - `listen` 指定 Nginx 监听的端口号和协议。
    - `server_name` 是你的域名或服务器的 IP 地址。
    - `location` 是针对特定位置的配置。
    - `proxy_pass` 设置反向代理目标的地址。
    - `proxy_set_header` 用于设置请求头信息，确保转发后头信息正确传递。

3. 根据实际情况自定义配置，确保代理地址、请求头设置等正确配置。

## 步骤 3：检查配置并重启 Nginx

使用以下命令检查 Nginx 配置是否有语法错误：

```bash
sudo nginx -t
```

如果没有错误，重启 Nginx 服务以使更改生效：

```bash
sudo systemctl restart nginx
```

# 配置 SSL 证书

## 步骤 1：获取 SSL 证书

你可以从证书颁发机构（CA）购买 SSL 证书，或者使用免费的证书颁发机构（如 Let's Encrypt）来获取证书。

如果使用 Let's Encrypt 申请免费的证书，可以参考文章 [[笔记/环境部署/Let's Encrypt免费申请SSL证书\|Let's Encrypt免费申请SSL证书]]。

## 步骤 2：将证书文件放到服务器上

将你的 SSL 证书文件和私钥文件上传到服务器上。一般来说，这两个文件通常是 `.crt` 和 `.key` 格式的文件。

## 步骤 3：编辑 Nginx 配置文件

1. 打开 Nginx 的配置文件，一般位于 `/etc/nginx/nginx.conf` 或 `/etc/nginx/sites-available/default`。

2. 在配置文件中找到服务器块（`server`），并对其进行修改以启用 SSL：

    ```
    server {
        listen 443 ssl;
        server_name example.com;

        ssl_certificate /path/to/your_domain.crt;
        ssl_certificate_key /path/to/your_private_key.key;

        # 其他 SSL 配置项
    }
    ```

    - listen 443 ssl; 指定 Nginx 监听 SSL 加密的 443 端口。
    - ssl_certificate 指定你的 SSL 证书文件路径。
    - ssl_certificate_key 指定你的私钥文件路径。

3. 添加其他 SSL 配置（可选），如 SSL 协议版本、加密套件等。

## 步骤 4：检查配置并重启 Nginx

使用以下命令检查 Nginx 配置是否有语法错误：

```bash
sudo nginx -t
```

如果没有错误，重启 Nginx 服务以使更改生效：

```bash
sudo systemctl restart nginx
```