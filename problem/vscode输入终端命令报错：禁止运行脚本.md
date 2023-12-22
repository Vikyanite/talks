# vscode输入终端命令报错：禁止运行脚本

## 前言

因为最近打算用vscode写一些简单的脚本，然后在用终端输出初始化命令的时候报错了：

```
>cnpm init

cnpm : 无法加载文件 C:\Users\vic\AppData\Roaming\npm\cnpm.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
所在位置 行:1 字符: 1
+ cnpm install --save-dev electron
+ ~~~~
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```

## 正文

原因是因为我们的vscode的终端默认打开的是powershell（没有管理员权限），所以我们没办法运行一些命令，是因为没权限。

所以我们只需要打开 Windows PowerShell（管理员），执行命令：`set-ExecutionPolicy RemoteSigned`即可。

然后我们再打开vscode输入`get-ExecutionPolicy`，之后出现`RemoteSigned`就是成功啦~

```
PS C:\WINDOWS\system32> get-ExecutionPolicy
RemoteSigned
```

完结撒花~