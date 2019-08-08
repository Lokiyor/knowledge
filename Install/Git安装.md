# Git安装
## Linux版本
* 直接执行以下命令，简单粗暴:
```
$ yum install git
```
## Window版本
1. 下载git: [https://git-scm.com/download/win](https://git-scm.com/download/win)
2. 点击安装程序进行安装
* 一些选项(基本都是默认值,按需选择):
    * Use Vim (The ubiquitous text editor) as Git's default editor  (使用 Vim 作为 Git 的默认编辑器)
    * Use Git from the Windows Command Prompt (这个选项被认为是安全的，因为它只向PATH添加一些最小的 Git包，以避免使用可选的Unix工具混淆环境。 您将能够从 Git Bash 和 Windows 命令提示符中使用 Git。)
    * Use the OpenSSL library (使用 OpenSSL 库，服务器证书将使用ca-bundle.crt文件进行验证。)
    * Checkout Windows-style,commit Unix-style line endings (在检出文本文件时，Git会将LF转换为CRLF。当提交文本文件时，CRLF将转换为LF。 对于跨平台项目，这是Windows上推荐的设置（“core.autocrlf”设置为“true”）)
    * Use MinTTY (the default terminal of MSYS2) (Git Bash将使用MinTTY作为终端模拟器，该模拟器具有可调整大小的窗口，非矩形选区和Unicode字体。 Windows控制台程序（如交互式Python）必须通过'winpty'启动才能在MinTTY中运行。)
    * Enable file system caching (启用文件系统缓存) 和 Enable Git Credential Manager (启用Git凭证管理器)