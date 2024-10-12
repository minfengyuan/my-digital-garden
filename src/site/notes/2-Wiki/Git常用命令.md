---
{"dg-publish":true,"title":"Git常用命令","created":"2024-01-16 15:13","updated":"2024-09-25 16:22","tags":["tool"],"dg-path":"Wiki/Git常用命令.md","permalink":"/Wiki/Git常用命令/","dgPassFrontmatter":true,"noteIcon":"1"}
---


本文粗略记录笔者关于使用 Git 的主要命令。

## 配置
### 账户
```bash
git config --global user.name "your name"
git config --global user.email "your email address"
```

### 代理
1. Socks5

	```bash
	git config --global http.proxy socks5://127.0.0.1:1080
	git config --global https.proxy socks5://127.0.0.1:1080
	```

2. http/https

	```bash
	git config --global http.proxy http://127.0.0.1:1080
	git config --global https.proxy https://127.0.0.1:1080
	```

3. 重置

	```bash
	git config --global --unset http.proxy
	git config --global --unset https.proxy
	```

## 基础命令

### Pull
	描述：用于从远程获取代码并合并本地的版本。

命令使用：

```bash
git pull <远程主机名> <远程分支>:<本地分支>
```

相关命令：

```bash
# 将远程主机origin的master分支拉取过来，与本地的branchtest分支合并。
git pull origin master:branchtest

#如果远程分支是与当前分支合并，则冒号后面的部分可以省略。
git pull origin master
```

### Commit
	描述：将暂存区中的更改记录到仓库历史中。

命令使用：
```bash
# 查看当前状态
git status

# 添加更改到暂存区
git add <文件名或路径>
# 或添加所有更改的文件
git add .

# 提交更改
git commit -m "提交信息"
```

相关命令：
```bash
# 修改最后一次提交
git commit --amend [-m "修正后的提交信息"]

# 撤销提交（保留更改）
git reset --soft HEAD~
# 撤销提交（移除更改）
git reset --hard HEAD~
# 撤销n次提交
git reset --soft HEAD~[n]
```

### Push
	描述：

命令使用：

相关命令：

### Stash
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

### Submodule
	以库或模块的形式在项目中使用第三方代码，又和自己的代码隔离

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

## 常见场景

### Git 仓库提交分支到另一个仓库
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

### 克隆远程仓库的指定分支
```bash
git clone -b branchA https://github.com/.../repo.git
```

### 拉取另一个远程仓库的指定分支
1. 首先在本地仓库添加远程仓库地址

    ```bash
    git remote add origin/<branch>  https://github.com/.../xxx.git
    ```

2. 然后拉取指定分支

    ```bash
    git pull origin <branch>
    ```

### 撤销操作
1. **撤销最近的提交**：
   - 使用 `git reset` 命令，后面跟上 `HEAD~` 可以撤销最近的一次提交。
   - 软重置（不撤销工作目录中的改动）：`git reset --soft HEAD~`
   - 混合重置（撤销提交，但保留改动在暂存区）：`git reset --mixed HEAD~`（默认选项）
   - 硬重置（撤销提交，不保留改动）：`git reset --hard HEAD~`

2. **撤销对文件的修改**：
   - 使用 `git checkout -- <file>` 可以撤销对某个文件的修改。

3. **撤销对暂存区的更改**：
   - 使用 `git reset <file>` 可以撤销对暂存区的更改。

4. **撤销合并**：
   - 如果你合并了错误的分支，可以使用 `git merge --abort` 来撤销合并操作。

5. **撤销 rebase**：
   - 如果你正在进行 rebase 操作，可以使用 `git rebase --abort` 来撤销 rebase。

6. **撤销已推送的提交**：
   - 如果你已经将错误的提交推送到了远程仓库，可以使用 `git push --force` 来强制推送一个撤销操作，但请注意这可能会影响其他协作者。

7. **使用 `git reflog` 查看历史操作**：
   - `git reflog` 可以查看所有的 HEAD 变动记录，包括撤销操作。

8. **使用 `git revert` 撤销特定提交**：
   - `git revert <commit-hash>` 可以创建一个新的提交，这个提交会撤销之前特定提交的更改。

请谨慎使用这些命令，因为某些操作可能会影响你的代码历史和协作者的工作。在执行撤销操作之前，最好备份你的工作目录。
