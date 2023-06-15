# ncm格式转换成普通音乐格式脚本
这个是我问的ai。

# 
是的，Windows中可以使用"拖放"操作将一个或多个文件拖到应用程序图标上，然后启动该应用程序并打开这些文件。

要使用脚本实现这个过程，首先需要确定要拖放的文件和目标应用程序的路径和名称。一般来说，可以使用 PowerShell 脚本编写这样的程序。

以下是一个简单的 PowerShell 脚本示例，该脚本允许用户通过拖放文件使用所选的应用程序打开它们：
```sh
$files = $args
$app = "C:\path\to\application.exe"

foreach ($file in $files) {
   & $app $file
}
```
将上述代码粘贴到一个新的 PowerShell 脚本文件中并保存。然后可以通过拖放文件到该脚本文件上来运行该脚本，它将使用指定的应用程序打开拖放的文件。

请注意，由于安全限制，必须将 PowerShell 的执行策略更改为允许执行脚本，否则该脚本将无法运行。在 PowerShell 中，可以使用以下命令修改执行策略：
```sh
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
```
希望这能对您有所帮助。