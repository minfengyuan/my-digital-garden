---
{"dg-publish":true,"title":"SSH使用教程","created":"2024-01-16 15:16","updated":"2024-10-12 14:30","tags":["tool"],"dg-path":"Tutorials/SSH使用教程.md","permalink":"/Tutorials/SSH使用教程/","dgPassFrontmatter":true,"noteIcon":"1"}
---


本文粗略记录了关于 SSH 命令的安装和常见使用。

# 安装

1. 安装 ssh 服务

    ```bash
    apt-get install openssh-server
    ```

2. 修改 ssh 配置,允许 root 登录

	一般进入容器时使用的都是 root 账号，但是 ssh 默认是禁止 root 账号使用密码远程登录的，所以需要修改 ssh 配置文件使其允许：

    ```bash
    vim /etc/ssh/sshd_config
    # 将PermitRootLogin的值从withoutPassword改为yes（去掉前面的#号）
    # 如果没有vim可以安装个：
    apt-get install vim
    ```

3. 启动 ssh 服务

    ```bash
    service ssh start
    ```

4. 判断 ssh 服务是否成功启动

    ```bash
    在终端中输入：ps -e|grep ssh，可以查看是否启动成功，如果有sshd，就说明启动成功。

    $ sudo ps -e | grep ssh

     4031 ?        00:00:00 sshd------对应服务器端

    sshd表示ssh-server启动了

    另外还可以查看网络连接状态
    sudo netstat -tnlp
    ```

# 命令格式

```bash
ssh [OPTIONS] [-p PORT] [USER@]HOSTNAME [COMMAND]
```

# 免密登录/SSH 密钥登录

1. 在客户端生成公私钥

    ```bash
    ssh-keygen
    ```

2. 上传公钥到服务器
	
	如果是**Linux & Unix**系统，输入下列命令：

     ```bash
     ssh-copy-id -i ~/.ssh/id_rsa.pub user@hosts
     ```
	如果是 Windows 系统或者无网络环境的情况下，输入下列命令：

    ```bash
    cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys && chmod 700 ~/.ssh"
    ```
3. [可选] 修改服务器 sshd_config 配置文件

	要允许使用密钥（公钥/私钥）登录，需要修改 `/etc/ssh/sshd_config` 文件中以下几个配置：
    
     - 将 `PubkeyAuthentication` 设置为 `yes`，表示启用公钥认证方式。
     - 将 `AuthorizedKeysFile` 设置为存储公钥的路径和文件名，例如：`AuthorizedKeysFile ~/.ssh/authorized_keys`，表示公钥文件存储在当前用户的 `.ssh` 目录下的 `authorized_keys` 文件中。
     - 将 `PasswordAuthentication` 设置为 `no`，表示禁用密码认证方式，只允许使用公钥认证方式登录。这样可以增加系统的安全性。

	要禁用 SSH 密钥身份验证，只需将 `/etc/ssh/sshd_config` 文件中的 `PasswordAuthentication`，`ChallengeResponseAuthentication`，`UsePAM` 指令改为 `no`。

4. 对本地 id_rsa 密钥设置密码

	我们可以使用命令对本地已有的 id_rsa 密钥进行加密:
   ```bash
   ssh-keygen -p -f ~/.ssh/id_rsa
   ```
   命令中的 `-p` 参数表示修改密码，`-f` 参数指定要修改密码的密钥文件。完成后，id_rsa 密钥文件就被加密了。下次使用该密钥时，需要输入密码才能使用。

	如果想要去除本地 id_rsa 密钥的密码，可以使用以下命令：
   ```bash
   ssh-keygen -p -f ~/.ssh/id_rsa -P <old_password> -N ""
   ```
   命令中的 `-p` 参数表示修改密码，`-f` 参数指定要修改密码的密钥文件，`-P` 参数指定原来的密码，`-N` 参数指定新的密码。在这个命令中，我们将新密码设置为空字符串，这样就相当于去除了原来的密码。完成后，id_rsa 密钥文件就被去除了密码。下次使用该密钥时，无需输入密码即可使用。

# Key Error

键入下列命令以清除密钥信息：

```bash
ssh-keygen -R [ip_address]
```