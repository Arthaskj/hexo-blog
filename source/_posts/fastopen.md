title: 快速启动器
category: 技能
date: 2019-08-14
index_img: /images/fastopen.assets/g1.gif

---

快速启动器

<!--more-->
<!--

 * @Author: 柯军
 * @Date: 2019-08-14 18:46:02
 * @Description: 
 -->
# 2019.2.14  快速启动器
### 快速启动器
- 每天工作需要打开很多软件
- 菜单栏固定了很多程序
- 桌面放置了很多快捷方式
- 需要快速打开指定文件夹

如果你符合以上几点，那么下面的教程对你可能会有一些帮助。

> 实现通过快捷键打开启动器，输入程序简称（可配置）或文件夹简称（可配置）即可打开对应程序与文件夹，不再需要桌面快捷方式以及菜单栏固定快捷方式。

### 效果展示
> **打开程序**

![gif1](/images/fastopen.assets/g1.gif)

> **打开文件夹**

![gif2](/images/fastopen.assets/g2.gif)

#### 1、创建vbs文件
首先创建一个`.vbs`后缀的文件,名称随意。

![代码路径](/images/fastopen.assets/6.png)

#### 2、编写代码
> 可以直接复制以下代码，将简称以及程序路径替换成你想要的即可。

```
Dim name,msg 
msg="请输入程序名称:"
name=Inputbox(msg,"快速启动器") 

Set wshell=CreateObject("WScript.Shell")

select case name
case "weixin"
wshell.Run """D:\软件\WeChat\WeChat.exe""",5,True
case "vscode"
wshell.Run """D:\软件\Microsoft VS Code\Code.exe""",5,True
case "weichat"
wshell.Run """C:\Users\Administrator\wechat\微信web开发者工具\微信web开发者工具.exe""",5,True
case "tim"
wshell.Run """F:\tim\Bin\QQScLauncher.exe""",5,True
case "notepad"
wshell.Run """D:\软件\Notepad++\notepad++.exe""",5,True
case "ydnote"
wshell.Run """F:\YoudaoNote\YoudaoNote.exe""",5,True
case "yxnote"
wshell.Run """D:\软件\Evernote.exe""",5,True
case "ssh"
wshell.Run """D:\DL\putty2186\putty64.exe""",5,True
case "hbulid"
wshell.Run """D:\soft\HBuilderX\HBuilderX.exe""",5,True
case "python"
wshell.Run """D:\软件\PyCharm Community Edition 2018.2.2\bin\pycharm64.exe""",5,True
case "color"
wshell.Run """D:\DL\downloadhistory\ColorCop.exe""",5,True
case "rp"
wshell.Run """D:\软件\Axure\Axure RP 8\AxureRP8.exe""",5,True
case "ps"
wshell.Run """C:\Program Files\Adobe\Photoshop CS6\Photoshop.exe""",5,True
case "mysql"
wshell.Run """D:\DL\navicat+for+mysql10.0.11简体中文破解版\navicat for mysql10.0.11简体中文破解版\Navicat for MySQL\navicat.exe""",5,True
case "markdown"
wshell.Run """D:\软件\Typora\Typora.exe""",5,True
case "vss"
wshell.Run """D:\vs\Common7\IDE\devenv.exe""",5,True
case "sql"
wshell.Run """C:\Program Files (x86)\Microsoft SQL Server\100\Tools\Binn\VSShell\Common7\IDE\Ssms.exe""",5,True
case "mstsc"
wshell.Run """mstsc""",5,True
case "email"
wshell.Run """D:\Program Files\Foxmail 7.2\Foxmail.exe""",5,True
case "iis"
wshell.Run """%windir%\system32\inetsrv\InetMgr.exe""",5,True
case "jsq"
wshell.Run """%windir%\system32\calc.exe""",5,True
case "note"
wshell.Run """%windir%\system32\notepad.exe""",5,True
case "dindin"
wshell.Run """D:\软件\DingDing\main\current_new\DingTalk.exe""",5,True
case "word"
wshell.Run """C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Microsoft Office 2013\Word 2013""",5,false
case "excel"
wshell.Run """C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Microsoft Office 2013\Excel 2013""",5,false
case "ppt"
wshell.Run """C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Microsoft Office 2013\PowerPoint 2013""",5,false

' 如果路径是文件夹，最后的参数为false
case "project"
wshell.Run """F:\Project1""",5,false
case "xphf"
wshell.Run """F:\Xinpin_HF""",5,false
case "xphs"
wshell.Run """F:\Xinpin_HSYH""",5,false
case "xpmain"
wshell.Run """F:\V5_XinPing""",5,false
case "xpzs"
wshell.Run """F:\Xinpin_ZS""",5,false
case "xpwk"
wshell.Run """F:\Xinpin_WK""",5,false
case "xpjh"
wshell.Run """F:\Xinpin_JH""",5,false
case "xpgd"
wshell.Run """F:\Xinpin_GD""",5,false


case else
' Msgbox("No Program")
end select
Set wshell = Nothing
```

#### 3、程序路径获取
> 将想加入快捷启动器的程序的路径填入代码中的位置，并设置好简称就可以在启动器中使用简称打开程序

![路径获取](/images/fastopen.assets/2.png)
![代码路径](/images/fastopen.assets/4.png)

#### 4、脚本快捷键设置
> 在此处设置快捷键后就可以使用快捷键打开启动器，建议快捷键设置的特殊一些，这样不会与其他程序内的快捷键冲突

![代码路径](/images/fastopen.assets/3.png)


#### 4、bat程序版本
![bat版本](/images/fastopen.assets/7.png)
```
CHCP 65001
set /p input=请输入程序代码：
if "%input%"=="weixin" (goto weixin) 
if "%input%"=="weichat" (goto weichat)
if "%input%"=="tim" (goto tim)
if "%input%"=="vscode" (goto vscode)
if "%input%"=="google" (goto google) 
if "%input%"=="notepad" (goto notepad) 
if "%input%"=="ydnote" (goto ydnote) 
if "%input%"=="yxnote" (goto yxnote) 
if "%input%"=="hbulid" (goto hbulid) 
if "%input%"=="python" (goto python) 
if "%input%"=="color" (goto color) 
if "%input%"=="rp" (goto rp) 
if "%input%"=="ps" (goto ps) 
if "%input%"=="mysql" (goto mysql) 
if "%input%"=="markdown" (goto markdown) 
if "%input%"=="ssh" (goto ssh) 
if "%input%"=="vss" (goto vss) 
if "%input%"=="sql" (goto sql) 

if "%input%"=="mstsc" (goto mstsc) 

echo 你输入不正确
REM pause

:mstsc
start mstsc
exit

:sql
"C:\Program Files (x86)\Microsoft SQL Server\100\Tools\Binn\VSShell\Common7\IDE\Ssms.exe"
exit

:vss
D:\vs\Common7\IDE\devenv.exe
exit

:ssh
D:\DL\putty2186\putty64.exe
exit

:markdown
D:\软件\Typora\Typora.exe
exit

:mysql
"D:\DL\navicat+for+mysql10.0.11简体中文破解版\navicat for mysql10.0.11简体中文破解版\Navicat for MySQL\navicat.exe"
exit

:ps
"C:\Program Files\Adobe\Photoshop CS6\Photoshop.exe"
exit

:rp
"D:\软件\Axure\Axure RP 8\AxureRP8.exe"
exit

:color
D:\DL\downloadhistory\ColorCop.exe
exit

:python
"D:\软件\PyCharm Community Edition 2018.2.2\bin\pycharm64.exe"
REM pause
exit

:hbulid
D:\soft\HBuilderX\HBuilderX.exe
REM pause
exit

:weixin
D:\软件\WeChat\WeChat.exe
REM pause
exit

:weichat
C:\Users\Administrator\wechat\微信web开发者工具\微信web开发者工具.exe
exit

:tim
F:\tim\Bin\QQScLauncher.exe
exit

:vscode
"D:\软件\Microsoft VS Code\Code.exe"
exit

:notepad
D:\软件\Notepad++\notepad++.exe
exit

:google
C:\Users\Administrator\AppData\Local\Google\Chrome\Application\chrome.exe
exit

:ydnote
F:\YoudaoNote\YoudaoNote.exe
exit

:yxnote
D:\软件\Evernote.exe
exit
```

### 总结

> 程序代码可以从文章中复制或扫描下面二维码询问获取。

![微信二维码](/images/fastopen.assets/wechatcode.png)