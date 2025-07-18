<h1 align="center"> 使用 GPG 为你的 Commit 签名</h1>

## 简介
一般情况下，我觉得你不应该没事找事用 GPG 签名。🤓\
毕竟 GitHub 上那么多大佬项目也基本没用 GPG 签名。\
本指南将帮助你完成以下操作：
- 使用 Git Bash 创建 GPG 密钥
- 配置 GPG 到 GitHub 账号
- 使 Visual Studio 支持 GPG 签名提交（需要 Gpg4win）

## 目的
- 使用 GPG 进行强身份验证。在 GitHub 上的 Commit 会显示 Verified 已验证。
- 即使你后来删除了 GitHub 绑定的旧邮箱，只要提交已通过 GPG 验证，依然会计入贡献图（contributions）。

## 创建 GPG 并配置到 GitHub
我将介绍使用 GitBash 创建GPG并配置到 GitHub。\
也会介绍 Visual Studio 中如何使用 GPG。
### 创建 GPG
打开 Git Bash 并打开你的项目文件夹：

    cd path/to/your-project

> 💡如果你为全局配置，可以不打开项目文件夹。

生成新的 GPG 密钥：

    gpg --full-generate-key

- 选择密钥类型：建议默认 1（RSA and RSA）
- 密钥长度：输入 4096
- 有效期：可输入 0 表示永久有效
- 输入你的 GitHub 邮箱地址（用于匹配账号）
- 设置名称（即 GPG 密钥的标识名称，可包含空格）
- 设置密码（可留空）

查看生成的 GPG 密钥 ID：

    gpg --list-secret-keys --keyid-format LONG

输出类似：

    ------------------------------------
    sec   rsa4096/ABCD1234EFGH5678 2025-07-18 [SC]
          ABCD1234EFGH5678...
    uid           [ultimate] Your Name <your@email.com>

记下 ABCD1234EFGH5678，这就是你的 GPG Key ID。

### 配置到 GitHub
导出公钥：

    gpg --armor --export YOUR_KEY_ID

将`YOUR_KEY_ID`替换为你的 Key ID。

输出类似：

    -----BEGIN PGP PUBLIC KEY BLOCK-----

    mQGNBGh6GLwBDAD3yPijIka4ThVY6kRkpUC5kCBstmnc7nXMoQmkZYI4tQ6V+DBF
    中间省略...
    J1VwnGiXwoldHSDZJwkMouo+uRnPVphjV7TTBz7z+thbs8ggKcZkNmQ4tKRmpVBv
    cNTd58SWSuCP3Bp/JLMv3g==
    =3vAg
    -----END PGP PUBLIC KEY BLOCK-----

复制从`-----BEGIN PGP PUBLIC KEY BLOCK-----`开始的内容，添加到 GitHub：
- 打开 GitHub → Settings → SSH and GPG Keys → GPG keys → New GPG key。
- 粘贴上一步导出的公钥，保存即可。

### 在 Git 中启用 GPG 签名

在 Git Bash 中输入：

    git config --global user.signingkey YOUR_KEY_ID
    git config --global commit.gpgsign true

如果只想为一个项目启用 GPG 输入：

    git config user.signingkey YOUR_KEY_ID
    git config commit.gpgsign true

🎉最后，你可以提交一个 Commit 测试是否生效。

### 为 Visual Studio 配置 GPG
Visual Studio 的 Git 使用 Windows 原生环境，因此需要通过 Gpg4win 来使用 GPG。

#### 为 Windows 安装 Gpg4win
- 下载并安装 [Gpg4win](https://gpg4win.org/download.html) 
- 安装时勾选组件 GnuPG 和 Kleopatra
- 安装完成后重启电脑确保生效

#### 导出 GPG 文件
在 Git Bash 中导出密钥对：

    # 导出私钥
    gpg --armor --export-secret-keys YOUR_KEY_ID > private.asc

    # 可选：导出公钥，之后一起做备份保存。
    gpg --armor --export YOUR_KEY_ID > public.asc

#### 导入 GPG 到 Kleopatra
打开 Kleopatra → 文件 → 导入 → 选择 private.asc。\
成功导入后，你将在密钥列表中看到刚导入的 GPG 密钥。

> 💡如果你没有使用 Git Bash 创建 GPG，那么你可以直接使用 Kleopatra 来创建。

#### 设置 VS 使用的 GPG 路径
打开 Visual Studio。在 菜单栏 > 视图 (View) > 终端 (Terminal) 打开终端，一般默认为 PowerShell。

如果 VS 的路径没有正确设置，根据你自己的 Gpg4win 安装路径，输入：

    git config --global gpg.program "C:/Program Files (x86)/GnuPG/bin/gpg.exe"

只为某个项目设置时，先打开项目目录，然后输入：

    git config gpg.program "C:/Program Files (x86)/GnuPG/bin/gpg.exe"

🎉最后，你可以通过 Visual Studio 的 Git 工具提交一个 Commit 测试是否生效。