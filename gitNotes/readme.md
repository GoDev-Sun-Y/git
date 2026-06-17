# Git 基础操作笔记
 
---
> <img src="../images/git工作流程图.jpg" width="600" style="display: block; margin: 0 auto;" /> 
## 下载 Git Bash

- 从官网下载并安装 Git Bash。

---

## 配置 Git 用户信息

```bash
git config --global user.name "github名"
git config --global user.email "github邮箱"
```

> **说明**：  
> - `--global` 表示全局配置，对所有项目生效。  
> - 若不添加引号内的内容（如只输入 `git config --global user.name`），则用于**查看**当前配置值。

---

## 创建 .bashrc 文件（可选，用于自定义别名）

```bash
touch ~/.bashrc
```

---

### 设置常用别名（alias）

#### 1. 用于输出 Git 提交日志（美化版）

```bash
alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'
```

> **作用**：  
> - `--pretty=oneline`：每条提交显示为一行。  
> - `--all`：显示所有分支的提交。  
> - `--graph`：显示分支拓扑图。  
> - `--abbrev-commit`：缩短提交哈希值。

#### 2. 用于输出当前目录所有文件及基本信息

```bash
alias ll='ls -al'
```

> **作用**：  
> - `ls -al`：列出所有文件（包括隐藏文件），并显示详细信息。

---

## 初始化一个 Git 仓库

### 步骤：

1. **新建文件夹**，右键选择 "Git Bash Here" 打开终端。
2. 执行初始化命令：

```bash
git init
```

> **作用**：  
> - 将当前文件夹变为 Git 可管理的仓库（会生成 `.git` 隐藏目录）。

---


# Git 基础命令速查笔记

## 一、工作区 → 暂存区 → 仓库流程
工作区 `git add` 暂存区 `git commit` 仓库（新文件默认状态为 `untracked`）

## 二、常用命令及说明

### 1. 查看状态：`git status`
- 作用：查看文件归属状态（可直接使用，无需额外参数）

### 2. 添加到暂存区：`git add`
- 语法：`git add 文件名`
- 扩展：文件名可替换为 `.`（当前目录所有文件）或**正则**匹配

### 3. 提交到仓库：`git commit`
- 语法：`git commit -m "注释"`
- 说明：`-m` 后跟提交备注，需为字符串

### 4. 查看提交历史：`git log` / `git-log`（推荐）
- 作用：查看项目的提交历史
- 优化：笔记标注 `git-log` 更好用（可能是自定义别名或简化版，见前文）
- 操作提示：可**使用它然后复制**提交记录（如版本回退时）

### 5. 版本回退：`git reset --hard`
- 语法：`git reset --hard commitID`
- 作用：回退到指定 `commitID` 对应的版本
- 注意：`commitID` 可从 `git log`/`git-log` 中获取

### 6. 查看操作记录：`git reflog`
- 作用：查看项目操作记录（含版本回退、分支切换等历史）

### 7. 忽略文件：`.gitignore`
- 操作：创建 `.gitignore` 文件
- 作用：管理不希望提交到暂存区的文件（如日志、依赖包、临时文件等）

---

> 注：版本回退需谨慎，`--hard` 会丢失未提交的修改，建议先备份。 


# Git 分支操作与冲突解决笔记（修订版）

## 一、Git 分支操作命令

| 命令                  | 作用                                                                 | 备注                                                                 |
|-----------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| `git branch`          | 查看**本地**所有分支（当前分支前有 `*` 标记）                         | 仅显示本地分支                                                       |
| `git branch <分支名>` | 创建新分支（基于当前所在分支）                                       | 新分支无文件，需 `checkout` 切换后才会切换到该分支的代码              |
| `git checkout <分支名>`| 切换到已有分支                                                       | 也可切换标签/提交（如 `git checkout <commit-hash>`）                 |
| `git checkout -b <分支名>`| 创建新分支并立即切换到该分支                                   | 等价于 `git branch <分支名> && git checkout <分支名>`                |
| `git merge <分支名>`  | 将 `<分支名>` 合并到当前所在分支                                     | 若冲突，需手动解决后执行 `git add` + `git commit`                    |
| `git branch -d <分支名>`| 删除**已合并**的本地分支                                             | 未合并不允许删除，需用 `-D` 强制删除                                  |
| `git branch -D <分支名>`| 强制删除**未合并**的本地分支                                         | 慎用，会丢失未合并的修改                                             |

## 二、解决冲突流程

1. 打开冲突文件（Git 会标记冲突区域：  
   - `<<<<<<< 分支A内容`  
   - `=======`  
   - `>>>>>>> 分支B内容`  
2. 手动修改，保留需要的代码（**删除冲突标记**）；  
3. 保存文件；  
4. 执行 `git add <冲突文件>`（将解决后的文件加入暂存区）；  
5. 执行 `git commit`（无需额外信息，Git 会自动生成合并提交信息）。

## 三、补充说明

- 若需查看远程分支，可执行 `git branch -r`；查看所有分支（本地+远程）用 `git branch -a`。  
- 合并分支前，建议先提交当前分支的修改，避免冲突风险。 


# 远程仓库 SSH 配置笔记（修正版）

## 一、配置公钥 SSH（Gitee）

### 1. 生成 RSA 密钥对
```bash
ssh-keygen -t rsa
```

### 2. （可选）备份旧 SSH 目录
```bash
mv ~/.ssh ~/.ssh.bak2
```


### 3. 再次生成 RSA 密钥（如已备份）
```bash
ssh-keygen -t rsa
```

### 4. 查看并复制公钥
```bash
cat ~/.ssh/id_rsa.pub
```

### 5. 验证是否成功连接 Gitee
```bash
ssh -T git@gitee.com
```
---

## 二、GitHub 密钥配置法（推荐使用 ed25519）

> **附注**：ed25519 是目前更推荐的安全密钥算法。

### 1. 生成 ed25519 密钥对
```bash
# <mark>修正：原参数为 `-c`，已修正为 `-C`（大写）</mark>
ssh-keygen -t ed25519 -C "your_email@example.com"
```
> 同时建议将“邮箱”替换为实际邮箱地址。

### 2. 查看并复制公钥
```bash
cat ~/.ssh/id_ed25519.pub
```
### 3. 验证是否成功连接 GitHub
```bash
ssh -T git@github.com
```

---

## 三、补充说明

- **RSA vs ed25519**：  
  - RSA 是传统算法，兼容性好，但密钥较长，安全性略低。  
  - ed25519 是现代椭圆曲线算法，**更安全、更快、密钥更短**，推荐优先使用。


# Git 命令整理与错误修正

## 一、关联本地与远程仓库
```bash
git remote add origin <远程仓库URL>
# 示例：git remote add origin https://github.com/yourname/repo.git
```

---

## 二、推送本地分支到远程

```bash
git push <远端名> <本地分支名>:<远程分支名>
```
---
## 三、克隆仓库
```bash
git clone <仓库URL> [本地目录名]
# 示例：git clone git@github.com:yourname/repo.git myrepo
```

---

## 四、拉取与抓取
### 拉取
```bash
git pull origin main
```
### 抓取
```bash
git fetch origin <分支名>
# 示例：git fetch origin main
```

---
