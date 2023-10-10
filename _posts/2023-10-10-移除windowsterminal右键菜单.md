---
tag: [vscode,c++]
description: vscode远程调试
---
喵
<!--more-->

# 修改注册表即可
将以下代码保存为reg文件,合并.
```markdown
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Shell Extensions\Blocked]
"{02DB545A-3E20-46DE-83A5-1329B1E88B6B}"="WindowsTerminal"

```


https://github.com/microsoft/terminal/issues/7008
