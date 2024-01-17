---
{"dg-publish":true,"title":"Docker常用命令","created":"2024/01/16, 15:12","updated":"2024/01/16, 15:15","tags":["ubuntu"],"dg-path":"环境部署/Docker常用命令.md","permalink":"/环境部署/Docker常用命令/","dgPassFrontmatter":true,"noteIcon":""}
---


本文简单记录 Docker 服务常用的命令。

# Docker 服务

1. 启动 Docker 守护进程

  ```bash
  systemctl daemon-reload
  ```

2. Docker 启动命令：

  ```bash
  systemctl start docker
  ```

3. 查看 docker 服务是否启动

  ```bash
  ps -ef | grep docker
  ```

4. 查看 docker 启动的服务：

  ```bash
  docker ps
  ```

5. 停止 docker 服务

  ```bash
  systemctl stop docker
  ```

6. 重启 docker 服务

  ```bash
  systemctl restart docker
  ```

# 容器的导入/导出/删除

1. 查看镜像（必须是正在运行中的）

    ```bash
    docker ps
    ```

2. 导出某个容器

    ```bash
    docker export $container_id > [容器快照名.tar]
    ```
 
3. 导入某个容器

    ```bash
    cat [容器快照名.tar] | docker import - [IMAGE]
    ```

4. 保存容器到镜像

    ```bash
    docker commit [OPTIONS] [CONTAINER ID] [IMAGE]
    ```

    OPTIONS 说明：
    - **a**: 提交的镜像作者；
    - **c**: 使用 Dockerfile 指令来创建镜像；
    - **m**: 提交时的说明文字；
    - **p**: 在 commit 时，将容器暂停。

5. 删除容器

    ```bash
    docker rm -f [容器id]
    ```

# 镜像的导入/保存/载入/删除

1. 查看镜像

    ```bash
    docker images
    ```

2. 导入镜像

    ```bash
    cat [镜像文件] | docker import - [IMAGE]
    ```

3. 保存镜像

    ```bash
    docker save -o [要保存的镜像名字] [要保存的镜像]
    docker save REPOSITORY:TAG > /path/to/file.tar
    ```

4. 载入镜像

    ```bash
    docker load < [本地的镜像名字]
    ```

5. 删除镜像

    ```bash
    docker rmi -f [image_id]
    ```   

# 应用场景

## 进入容器

```bash
docker exec -it [container_name] bash
```

## 多个 Docker 挂载同一个宿主机的文件目录

第一个容器使用

```bash
docker run -it --privileged=true --name docker1 -v /data-volume/:/data-volume centos
```

第二个~第 N 个容器

```bash
docker run -it --privileged=true --name docker2 --volumes-from docker1 centos
```

主要参数说明:

- -privileged=true //代表赋予容器特权,可以对挂载的目录进行读写操作
- v /data-volume/:/data-volume //将宿主机的目录/data-volume 挂载到容器中的/data-volume 目录 (只需要第一个容器这样挂载，剩下的容器使用 --volumes-from 参数)
- -volumes-from //参数后面跟着的 docker1 是数据卷容器名字，也就是第一个容器的名字，这里第一个容器就被当做数据卷容器了。
- --name //容器的名字，必须唯一，与其他的容器名字不可重复