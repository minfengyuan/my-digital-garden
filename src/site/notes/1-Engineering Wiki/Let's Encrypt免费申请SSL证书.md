---
{"dg-publish":true,"title":"Let's Encrypt免费申请SSL证书","created":"2024-01-16 15:18","updated":"2024-01-30 11:02","tags":["tool"],"permalink":"/1-Engineering Wiki/Let's Encrypt免费申请SSL证书/","dgPassFrontmatter":true,"noteIcon":"1"}
---


Let's Encrypt 是免费、开放和自动化的证书颁发机构 (CA)。我们可以从 [Let's Encrypt](https://letsencrypt.org) 获得网站域名的免费的证书。

# 使用 Certbot 申请 SSL 证书

[Certbot](https://certbot.eff.org/) 是 Let’s Encrypt 官方推荐的获取证书的客户端，可以帮我们获取免费的 Let’s Encrypt 证书。Certbot 支持所有 Unix 内核的操作系统。

## 安装 Certbot

Certbot 官方推荐使用 snap 安装，当然你也可以使用 yum 或者 dnf。

1. 安装 snapd

   Snap 已经预装在 Ubuntu 16.04 (Xenial Xerus) 及更新发行版本，如果使用旧版，可以手动安装 snap :)

    ```bash
	  sudo apt update
	  sudo apt install snapd
    ```

    更多 OS 安装请参考 [文档](https://snapcraft.io/docs/installing-snapd)。

2. 移除旧版

   如果此前使用过其他包管理工具进行安装 certbot，推荐在安装前移除:

    ```bash
    # apt
    sudo apt-get remove certbot
    # dnf
    sudo dnf remove certbot
    # yum
    sudo yum remove certbot
    ```

3. 安装

   在命令行运行以下命令进行安装

    ```bash
    sudo snap install --classic certbot
    ```

4. 准备 certbot 命令

   创建软链以便在终端快捷使用 certbot 命令:

    ```bash
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    ```

5. 运行

   接下来，你就可以使用 certbot 来获取证书，或者对证书进行续约。具体细节请参考 [文档](https://certbot.eff.org/instructions)。

## 申请泛域名 SSL 证书以及配置自动续约

不管是申请还是续期，只要是通配符证书，只能采用 dns-01 的方式校验申请者的域名，也就是说每次续期证书我们都必须登录控制台添加对应的 TXT 记录才可以续期/申请成功，这样不免有些麻烦。好在 certbot 提供了一个 hook，可以调用域名提供商的 API 接口来添加 TXT 记录而无需人工干预，但是官方并不支持国内的大部分服务商，这样就要用到第三方的插件了。此处我们使用的是 `certbot-letencrypt-wildcardcertificates-alydns-au`，本文仅简单介绍，更多细节请参考项目 [文档](https://github.com/ywdblog/certbot-letencrypt-wildcardcertificates-alydns-au)。

1. 下载

    ```bash
    git clone https://github.com/ywdblog/certbot-letencrypt-wildcardcertificates-alydns-au
    cd certbot-letencrypt-wildcardcertificates-alydns-au
    chmod 0777 au.sh
    ```

2. 配置

   （1） domain.ini

    如果 domain.ini 文件没有你的根域名，请自行添加。

   （2） au.sh

    登录域名服务商的控制台获取 API 密钥，然后配置在 au.sh 文件中。

    - ALY_KEY 和 ALY_TOKEN：阿里云 [API key 和 Secrec 官方申请文档](https://help.aliyun.com/knowledge_detail/38738.html)
    - TXY_KEY 和 TXY_TOKEN：腾讯云 [API 密钥官方申请文档](https://console.cloud.tencent.com/cam/capi)
    - HWY_KEY 和 HWY_TOKEN: 华为云 [API 密钥官方申请文档](https://support.huaweicloud.com/devg-apisign/api-sign-provide.html)
    - GODADDY_KEY 和 GODADDY_TOKEN：GoDaddy [API 密钥官方申请文档](https://developer.godaddy.com/getstarted)

3. 申请证书

	首先我们测试是否有误：
	
    ```bash
    sudo certbot certonly \
    -d *.example.com \
    --manual --preferred-challenges dns \
    --dry-run \
    --manual-auth-hook "/脚本目录/au.sh php aly add" \
    --manual-cleanup-hook "/脚本目录/au.sh php aly clean"
    ```

	**注意：** --manual-auth-hook 和 --manual-cleanup-hook 有三个参数：

     - 第一个代表你要选择那种语言 (php/python)
     - 第二个参数代表你的 DNS 厂商 (aly/txy)
     - 第三个参数是固定的 (--manual-auth-hook 中用 add，--manual-clean-hook 中用 clean)

	比如你要选择 Python 环境，可以将 --manual-auth-hook 输入修改为 "/脚本目录/au.sh python aly add"，--manual-cleanup-hook 输入修改为 "/脚本目录/au.sh python aly clean"

	确认无误后，实际运行（去除 --dry-run 参数）：
    
    ```bash
    sudo certbot certonly \
    -d *.example.com \
    --manual --preferred-challenges dns \
    --manual-auth-hook "/脚本目录/au.sh php aly add" \
    --manual-cleanup-hook "/脚本目录/au.sh php aly clean"
    ```

	参数解释:

    - `certonly`：表示采用验证模式，只会获取证书，不会为 web 服务器配置证书
    - `--manual`：表示插件
    - `--preferred-challenges dns`：表示采用 DNS 验证申请者合法性（是不是域名的管理者）
    - `--dry-run`：在实际申请/更新证书前进行测试，强烈推荐
    - `-d`：表示需要为那个域名申请证书，可以有多个。
    - `--manual-auth-hook`：在执行命令的时候调用一个 hook 文件
    - `--manual-cleanup-hook`：清除 DNS 添加的记录

4. 续约

	手动续约

    ```bash
    sudo certbot renew
    ```

  	自动续约

    ```bash
    # 编辑文件
    vim /etc/crontab
    或配置了crontab编辑器的情况下
    crontab -e

    # 填入以下内容
    1 1 */1 * * sudo certbot renew --manual --preferred-challenges dns --deploy-hook "service nginx restart" --manual-auth-hook "/脚本目录/au.sh php aly add" --manual-cleanup-hook "/脚本目录/au.sh php aly clean"
    # 注意只有成功renew证书，才会重新启动nginx
    ```