---
{"dg-publish":true,"title":"Homebrew使用教程","created":"2023-12-21 08:45","updated":"2024-01-29 15:00","tags":["tool"],"dg-path":"环境部署/Homebrew使用教程.md","permalink":"/环境部署/Homebrew使用教程/","dgPassFrontmatter":true,"noteIcon":""}
---


本文简单记录安装 Homebrew 的步骤。

# 安装

## 方法一：brew 官网的安装脚本

执行下列命令

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

该方法极不稳定，原因是 https://raw.githubusercontent.com 访问不稳定。

## 方法二：brew 镜像安装脚本（推荐）

```bash
/bin/bash -c "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install.sh)"
```

# 更新

```bash
brew update
```

## 替换更新源

```bash
# 替换brew.git:
cd "$(brew --repo)"
# 中国科大:
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
# 清华大学:
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

# 替换homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
# 中国科大:
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
# 清华大学:
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

# 替换homebrew-bottles:
# 中国科大:
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
# 清华大学:
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile

# 应用生效:
brew update
```

如果你之前折腾过不少导致你的 Homebrew 有点问题，那么可以尝试使用如下方案：

```bash
# 诊断Homebrew的问题:
brew doctor

# 重置brew.git设置:
cd "$(brew --repo)"
git fetch
git reset --hard origin/master

# homebrew-core.git同理:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git fetch
git reset --hard origin/master

# 应用生效:
brew update
```

## 重置更新源 某些时候也有换回官方源的需求

```bash
# 重置brew.git:
cd "$(brew --repo)"$ git remote set-url origin https://github.com/Homebrew/brew.git

# 重置homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"$ git remote set-url origin https://github.com/Homebrew/homebrew-core.git
```

# Thanks

1. [Mac安装Homebrew的正确姿势](https://www.jianshu.com/p/e0471aa6672d?utm_campaign=hugo)
2. [update Homebrew 太慢，更换Homebrew的更新源](https://www.jianshu.com/p/b26c7bc14440)