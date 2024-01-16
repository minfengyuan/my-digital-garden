---
{"dg-publish":true,"title":"SSH使用教程","created":"2024/01/16, 15:16","updated":"2024/01/16, 15:16","tags":["tool"],"permalink":"/笔记/SSH使用教程/","dgPassFrontmatter":true}
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
选项说明：

>-1	强制只使用协议第一版  
-2 强制只使用协议第二版  
-4 强制只使用 IPv4 地址  
-6 强制只使用 IPv6 地址  
-A 允许转发认证代理的连接。可以在配置文件中对每个主机单独设定这个参数  
-a 禁止转发认证代理的连接  
-b BIND_ADDRESS 在拥有多个地址的本地机器上，指定连接的源地址  
-C 压缩所有数据。压缩算法与 gzip(1) 使用的相同  
-c {blowfish | 3des | des} 选择会话的密码算法。3des 是默认算法  
-c CIPHER_SPEC 另外, 对于协议第二版，这里可以指定一组用逗号隔开、按优先顺序排列的加密算法  
-D [BIND_ADDRESS:]PORT 指定一个本地主机动态的应用程序级的转发端口。工作原理是这样的，本地机器上分配了一个 socket 侦听 port 端口，一旦这个端口上有了连接，该连接就经过安全通道转发出去，根据应用程序的协议可以判断出远程主机将和哪里连接。目前支持 SOCKS4 和 SOCKS5 协议，而 ssh 将充当 SOCKS 服务器. 只有 root 才能转发特权端口。可以在配置文件中指定动态端口的转发  
-e ESCAPE_CHAR 设置 pty 会话的转义字符，默认为字符 ~。转义字符只在行首有效，转义字符后面跟一个点表示结束连接，后跟一个 control-Z 表示挂起连接，跟转义字符自己表示输出转义字符自身。把转义字符设为 none 则禁止 转义功能，使会话完全透明  
-F CONFIGFILE 指定 ssh 指令的配置文件，将忽略系统级配置文件 /etc/ssh/ssh_config 和用户级配置文件 ~/.ssh/config  
-f ssh 在执行命令前退至后台  
-g 允许远端主机连接本地的转发端口  
-I SMARTCARD_DEVICE 指定智能卡设备。智能卡里面存储了用户的 RSA 私钥  
-i IDENTITY_FILE 指定一个 RSA 或 DSA 认证所需的身份（私钥）文件。协议第一版的默认文件是 ~/.ssh/identity 以及协议第二版的 ~/.ssh/id_rsa 和 ~/.ssh/id_dsa 文件。可以同时使用多个 -i 选项，也可以在配置文件中指定多个身份文件  
-K 启用基于 GSSAPI 的身份验证和向服务器转发 GSSAPI 凭据  
-k 禁用向服务器转发 GSSAPI 凭据  
-L [BIND_ADDRESS:]PORT:HOST:HOSTPORT 将本地主机的地址和端口接收到的数据通过安全通道转发给远程主机的地址和端口  
-l LOGIN_NAME 指定登录远程主机的用户。可以在配置文件中对每个主机单独设定这个参数  
-M 将 ssh 客户端置于主模式进行连接共享。多个 -M 选项将 ssh 置于主模式，并在接受从连接之前进行确认  
-m MAC_SPEC 对于协议第二版，可以指定一组用逗号隔开，按优先顺序排列的 MAC (message authentication code) 算法  
-N 不执行远程命令，用于转发端口。仅限协议第二版  
-n 把 stdin 重定向到 /dev/null，防止从 stdin 读取数据。在后台运行时一定会用到这个选项  
-O CTL_CMD 控制主动连接多路复用主进程。参数 CTL_CMD 将被传递给主进程。CTL_CMD 可取值 check（检查主进程是否正在运行）和 exit（让主进程退出）  
-o OPTION 可以在这里给出某些选项，格式和配置文件中的格式一样。它用来设置那些没有单独的命令行标志的选项  
-p PORT 指定远程主机的端口。可以在配置文件中对每个主机单独设定这个参数  
-q 安静模式。消除大多数的警告和诊断信息  
-R [BIND_ADDRESS:]PORT:HOST:HOSTPORT 将远程主机上的地址和端口接收的数据通过安全通道转发给本地主机的地址和端口  
-S CTL_PATH 指定用于连接共享的控制套接字的位置  
-s 用于请求远程系统上的子系统调用。子系统是 SSH2 协议的一个特性，它有助于将 SSH 用作其他应用程序（如 sftp(1)）的安全传输。子系统通过远程命令指定  
-T 禁止分配伪终端  
-t 强制分配伪终端。这可用于在远程计算机上执行基于屏幕的任意程序，例如菜单服务。多个 -t 选项强制分配终端, 即使没有本地终端  
-V 显示版本信息并退出  
-v 冗详模式。打印关于运行情况的调试信息。在调试连接、认证和配置问题时非常有用。多个 -v 选项能够增加冗详程度，最多三个  
-W HOST:PORT 将客户端上的标准输入和输出通过安全通道转发给指定主机的端口  
-w LOCAL_TUN[:REMOTE_TUN] 指定客户端和服务端之间转发的隧道设备  
-X 允许 X11 转发，可以在配置文件中对每个主机单独设定这个参数  
-x 禁止 X11 转发  
-Y 启用受信任的 X11 转发。受信任的 X11 转发不受 X11 安全扩展控制的约束  
-y 使用 syslog(3) 系统模块发送日志信息。默认情况下，此信息被发送到 stderr

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
    cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys && chmod700 ~/.ssh"
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