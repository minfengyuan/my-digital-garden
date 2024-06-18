---
{"dg-publish":true,"title":"Git常用命令","created":"2024-01-16 15:13","updated":"2024-01-29 15:00","tags":["tool"],"permalink":"/1-Engineering Wiki/Git常用命令/","dgPassFrontmatter":true,"noteIcon":"1"}
---


本文粗略记录笔者关于使用 Git 的主要命令。

# 配置

## 账户

```bash
git config --global user.name "your name"
git config --global user.email "your email address"
```

## 代理

### Socks5

```bash
git config --global http.proxy socks5://127.0.0.1:1080
git config --global https.proxy socks5://127.0.0.1:1080
```

### http/https

```bash
git config --global http.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
```

### 重置

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

# 基础命令

## Pull

	描述：用于从远程获取代码并合并本地的版本。

命令使用：

```bash
git pull <远程主机名> <远程分支>:<本地分支>
```

相关命令：

```bash
# 将远程主机origin的master分支拉去过来，与本地的branchtest分支合并。
git pull origin master:branchtest

#如果远程分支是与当前分支合并，则冒号后面的部分可以省略。
git pull origin master
```

## Commit

	描述：

命令使用：

相关命令：

## Push

	描述：

命令使用：

相关命令：

# 进阶命令

## Stash

	描述：该命令将保存本地修改，并恢复干净的工作目录。
	应用场景：当想切换分支，但是当前分支有文件更改了，需要保持工作区干净才能切分支。

命令使用：

```bash
git stash
```

相关命令：

```bash
# 保存当前未commit的代码并添加备注
git stash save "备注的内容"

# 列出stash的所有记录
git stash list

# 删除stash的所有记录
git stash clear

# 应用最近一次的stash
git stash apply

# 应用最近一次的stash，随后删除该记录
git stash pop

# 删除最近的一次stash
git stash drop
```

# Git 仓库提交分支到另一个仓库

	假设有仓库repo_a和仓库repo_b，目的是要将仓库repo_a的branch_test分支提交到仓库repo_b。

1. 先将 repo_a 拉取到本地

    ```bash
    git clone https://github.com/.../repo_a.git
    ```

2. 创建一个分支，起点 master（如果已有分支跳过该步骤）

    ```bash
    git branch branch_test master
    ```

3. 添加 repo_b 的远程地址

    ```bash
    git remote add repo_b_branch https://github.com/.../repo_b.git
    ```

    可以查看已有的远程路径

    ```bash
    git remote -v
    ```

4. 推送 branch_test 分支到 repo_b

    如果要使用另外的分支名，例如 repo_b_new_branch，在分支后面使用冒号加新的分支名称

    ```bash
    git push repo_b_branch branch_test:repo_b_new_branch
    ```

5. 如果远程已有该分支，推送会报错

    需要先从远程拉取，在本地 merge 以后，可以提交。

# 克隆远程仓库的指定分支

```bash
git clone -b branchA https://github.com/.../repo.git
```

# 拉取另一个远程仓库的指定分支

1. 首先在本地仓库添加远程仓库地址

    ```bash
    git remote add origin/<branch>  https://github.com/.../xxx.git
    ```

2. 然后拉取指定分支

    ```bash
    git pull origin <branch>
    ```

# Git Submodule

	以库或模块的形式在项目中使用第三代码，又和自己的代码隔离

1. 创建一个文件夹用于存储第三方库

    ```bash
    mkdir lib
    cd lib
    ```

2. 将第三方库以 submodule 的方式注入到项目中

    ```bash
    git submodule add https://github.com/xxx/xxx.git
    ```

    第三方库将会被 clone 到文件夹中