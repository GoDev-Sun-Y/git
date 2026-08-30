# 码云（Gitee）生成 SSH 公钥完整指南

> 本文档基于实际截图整理，覆盖从 Git 基础配置到 Gitee 公钥绑定、连接测试的全流程。
> 示例环境：macOS（终端：`zhangxindeMacBook-Pro`），用户：张欣，邮箱：`13126555525@163.com`

---

## 一、前置准备：配置 Git 全局身份

> 在生成 SSH 公钥之前，先确保本机 Git 已经配置好用户名和邮箱，否则后续提交无法被识别身份。

打开终端（macOS / Linux）或 Git Bash（Windows），执行以下两条命令：

```bash
git config --global user.name "张欣"
git config --global user.email "13126555525@163.com"
```

**说明**：
- `--global` 表示全局生效，所有 Git 仓库都会使用这个身份。
- 邮箱建议与 Gitee / GitHub 注册邮箱保持一致，方便后续识别提交记录。
- 验证是否配置成功：

  ```bash
  git config --global --list
  ```

---

## 二、登录 Gitee 并进入 SSH 公钥设置页

### 2.1 进入个人设置

1. 打开 [Gitee 官网](https://gitee.com)，登录账号。
2. 点击右上角 **个人头像**。
3. 在下拉菜单中选择 **「设置」**。

### 2.2 进入 SSH 公钥页面

在左侧菜单的 **「安全设置」** 分组下，点击 **「SSH 公钥」**：

- 页面顶部会提示：**使用 SSH 公钥可以让你在你的电脑和 Gitee 通讯的时候使用安全连接（Git 的 Remote 要使用 SSH 地址）**。
- 页面中部会显示 **「您当前的 SSH 公钥数」**，可对已有公钥进行删除管理。
- 页面下部是 **「添加公钥」** 表单：
  - **标题**：给这把公钥起个名字（例：`MacBook-Pro` / `家里的win`）。
  - **公钥**：粘贴从本机生成的公钥内容。
  - 公钥必须以 `ssh-rsa`、`ssh-dss`、`ssh-ed25519`、`ecdsa-sha2-nistp256`、`ecdsa-sha2-nistp384`、`ecdsa-sha2-nistp521` 之一开头。

如果不知道如何生成公钥，可点击 **「怎样生成公钥」** 链接跳转到官方教程（见下一步）。

---

## 三、本机生成 SSH 密钥对

### 3.1 推荐方式：ED25519 算法（更安全、更短）

在终端执行：

```bash
ssh-keygen -t ed25519 -C "13126555525@163.com"
```

**参数说明**：

| 参数 | 含义 | 示例值 |
|------|------|--------|
| `-t` | 指定 key 类型 | `ed25519`（推荐）/ `rsa` / `dsa` |
| `-C` | 添加注释（一般用邮箱） | `"13126555525@163.com"` |

> **Windows 用户提示**：官方建议使用 **Windows PowerShell** 或 **Git Bash**，因为 CMD 默认没有 `cat`、`ls` 等命令。
>
> 传统 RSA 写法（兼容老系统）：
> ```bash
> ssh-keygen -t rsa -b 4096 -C "13126555525@163.com"
> ```

### 3.2 交互流程

执行命令后，终端会进行 3 次回车确认：

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/你的用户名/.ssh/id_ed25519):
# 1. 回车：使用默认路径保存私钥

Enter passphrase (empty for no passphrase):
# 2. 回车：私钥不设密码（如需更高安全性可输入密码）

Enter same passphrase again:
# 3. 回车：再次确认密码（或直接回车）
```

成功后输出示例：

```
Your identification has been saved in /Users/zhangxin/.ssh/id_ed25519
Your public key has been saved in /Users/zhangxin/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:ohDd0OK5WGZdx4g5T/j35HvJvlJ1GHv1hY+Ms16IC8I 13126555525@163.com
The key's randomart image is:
+--[ED25519 256]--+
|  .o.            |
|   .+oo          |
|  . .o. +        |
| . . . . + .     |
|  o . . S+ +     |
| . .  ..=o..+    |
|  .   ..=o..=o.  |
|   .  .o+..o o.  |
+----[SHA256]-----+
```

### 3.3 已存在密钥的处理

如果之前已经生成过同名的密钥，终端会提示：

```
/Users/fatboss/.ssh/id_ed25519 already exists.
Overwrite (y/n)?
```

- 输入 `y`：**覆盖**已有密钥（注意：覆盖后旧密钥对应的服务器授权会失效）。
- 输入 `n`：**保留**旧密钥，跳到下一步查看公钥即可。

---

## 四、查看并复制公钥内容

### 4.1 查看 `.ssh` 目录下的文件

```bash
ls ~/.ssh/
```

典型输出：

```
agent            config         id_ed25519       id_ed25519.pub
id_rsa           id_rsa.pub     known_hosts      known_hosts.old
```

- `id_ed25519`：**私钥**，**绝对不能泄露**，留在本机。
- `id_ed25519.pub`：**公钥**，需要上传到 Gitee。

### 4.2 读取公钥内容

```bash
cat ~/.ssh/id_ed25519.pub
```

输出形如：

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI...（一长串字符）... 13126555525@163.com
```

**复制要点**：
- 一整行内容**全部复制**，包括开头的 `ssh-ed25519` 和结尾的邮箱注释。
- **不要手动换行、不要添加空格**。
- 推荐用 `pbcopy`（macOS）一键复制到剪贴板：

  ```bash
  pbcopy < ~/.ssh/id_ed25519.pub
  # Windows (Git Bash) 用 clip：
  # clip < ~/.ssh/id_ed25519.pub
  # Linux 用 xclip：
  # xclip -selection clipboard < ~/.ssh/id_ed25519.pub
  ```

---

## 五、将公钥添加到 Gitee

回到 Gitee 的 **「添加公钥」** 表单：

1. **标题**：填一个易辨识的名字，建议包含设备 + 用途，例如：
   - `MacBook-Pro 个人`
   - `家里的win`
   - `公司办公电脑`
2. **公钥**：把刚才复制的公钥字符串粘贴进来。
3. 点击 **「确定」** 保存。

> ⚠️ 首次添加可能要求输入 Gitee 账户密码或短信验证。

添加成功后，在 **「您当前的 SSH 公钥数」** 列表中就能看到刚刚添加的公钥，包含：
- 公钥对应的邮箱 / 标题
- 算法（`SHA256:...`）
- 添加时间
- 最后使用时间
- 「删除」按钮（可随时移除）

---

## 六、验证 SSH 连接

回到终端，测试能否免密连接 Gitee：

```bash
ssh -T git@gitee.com
```

**首次连接**会提示：

```
The authenticity of host 'gitee.com (180.97.XX.XX)' can't be established.
ED25519 key fingerprint is SHA256:FQGCzlPEMbFskj81T0a9Rf9z0ej1A2Y0c8PX0Kqcfk8.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

- 输入 `yes` 并回车（会把 Gitee 的 host key 加入 `~/.ssh/known_hosts`）。

**成功响应**：

```
Hi 张欣! You've successfully authenticated, but GITEE.COM does not provide shell access.
```

看到 `successfully authenticated` 即表示配置完成 🎉。

---

## 七、常见问题与注意事项

### 7.1 修改 Gitee 账号但本机 git 用户名/邮箱未更新
提交时仍会显示旧用户。需要重新执行第一步的 `git config --global` 命令。

### 7.2 Permission denied (publickey)
按以下顺序排查：
1. `ls ~/.ssh/` 确认私钥存在。
2. `cat ~/.ssh/id_ed25519.pub` 确认公钥已正确添加到 Gitee。
3. 确认 Gitee 上公钥的「标题/邮箱」与本机一致。
4. macOS 上检查 `~/.ssh/` 目录权限：
   ```bash
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/id_ed25519
   chmod 644 ~/.ssh/id_ed25519.pub
   ```

### 7.3 一台电脑配置多个 Gitee / GitHub 账号
在 `~/.ssh/config` 中按 Host 区分：

```config
# Gitee 个人
Host gitee.com
    HostName gitee.com
    User git
    IdentityFile ~/.ssh/id_ed25519

# GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_github
```

### 7.4 Windows 注意事项
- 推荐使用 **Git Bash** 或 **PowerShell**，不要用 CMD。
- 如果使用 TortoiseGit 等 GUI 客户端，需在「网络 → SSH 客户端」中指定 Git 安装目录下的 `ssh.exe`。

---

## 八、流程速查

```
1. git config --global user.name / user.email       ← 配置身份
2. 登录 Gitee → 头像 → 设置 → SSH 公钥               ← 进入公钥页
3. ssh-keygen -t ed25519 -C "邮箱"                  ← 生成密钥
4. cat ~/.ssh/id_ed25519.pub → 复制                  ← 读取公钥
5. Gitee 「添加公钥」→ 粘贴 → 确定                  ← 绑定公钥
6. ssh -T git@gitee.com                             ← 验证连接
```

完成以上 6 步后，本机就能用 `git@gitee.com:用户名/仓库名.git` 这种 SSH 地址进行免密克隆、推送、拉取操作了。
