---
title: windows11偏好配置
date: 2022-12-03 12:00:00
categories:
  - 操作系统
tags:
  - windows
abbrlink: 91c4f57f 
cover:
---
  
# windows11偏好配置

## 1、Win11修改右键菜单为win10风格

1、首先用鼠标右键点击开始菜单，选择 windows 终端(管理员)，点击进入。

2、然后在终端应用程序里粘贴这串代码 ：

```powershell
reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```

3、恢复Win11右键菜单

```powershell
reg.exe delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /va /f
```

## 2、Win11修改C盘用户文件夹名称

一、目标

　　更改“C:/Users”目录下用户文件夹名称，尽量不影响大部分软件正常运行。

二、步骤

　　1.开启Administrator管理员账户

　　右击开始菜单按钮，打开“计算机管理”，选择”本地用户和组”，点击“本地用户和组-用户”，右侧可以看到Administrator用户，双击，将“账号已禁用”的对勾取消，点击确定，注销当前用户，可以看到Administrator用户出现了，点击进入Administrator账户。

![img](./assets/windows11偏好配置/202210221364027.png)

　　PS：有些用户的电脑是WIN10家庭版系统，可能没有本地用户和组这个选项，需要升级专业版系统。

　　2.重命名用户文件夹

　　1.用Administrator账户登录以后，再次打开“本地用户和组-用户”，右击原账户的中文名称，选择重命名，修改为想要的英文名称。双击原账户，进入后可修改全名，全名可保持中文名称，这个名称是显示在登录界面的名称，不影响实际路径。

![img](./assets/windows11偏好配置/202210221364028.png)

　　2.进入“C:\用户” 文件夹(其实真实路径是“C:\Users”)，重命名之前中文的用户文件夹，更改为英文，此名称与上一步的英文名称保持一致。

　　PS：此步如果出现重命名失败，文件夹内有运行的程序的情况，可能是原来的账号没有完全注销，需要重启电脑，直接登陆Administrator账户，千万不能再点原来的中文账户，点击后即使不登陆也会预加载很多程序，导致原用户文件夹被占用无法重命名。

　　3.修改注册表

　　1.“Win+R”打开运行，输入regedit，打开注册表管理器，

　　依次展开HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList，在ProfileList下的每个文件夹都对应系统中的用户，而文件夹中ProfileImagePath字段是指向每个用户文件夹的地址，一个个点击查看ProfileList下的文件夹，在右侧找到ProfileImagePath字段中包含原用户名的数据，双击修改ProfileImagePath的数据，将路径修改成新的用户文件夹名。

![img](./assets/windows11偏好配置/202210221364029.png)

　　2.重启电脑，登陆原来的账户，现在可以看到用户文件夹名称已经修改成功了，但系统中很多软件中包含了一些配置路径信息，这些信息很多保存在注册表中。修改注册表，将原来包含中文用户名称的注册表字段替换为英文，可避免很多软件报错的情况。这里推荐RegWorkshop，可以实现批量查找替换。

　　打开RegWorkshop，先点搜索，查找内容中输入原用户名，点击查找。

![img](./assets/windows11偏好配置/202210221364030.png)

　　查找出来很多结果，在结果中“Ctrl+A”全选结果，右键点击替换，替换为文本框中输入刚改的新用户名，点击替换。

![img](./assets/windows11偏好配置/202210221364031.png)

　　4.修改环境变量

　　依次点击此电脑右键-属性-高级系统设置-高级-环境变量，打开后依次查看各个字段，找到有原用户名的字段都替换成新的用户名，这样能防止有些软件报错。

　　三、后期问题处理

　　更改用户文件夹名称后绝大部分软件都能正常运行，少部分软件内设置的存储路径需要手动设置成新的路径。极少部分软件变成未注册状态，如CAD和SolidWorks，再次pj后可正常使用。

　　Administrator账户启用后有一定安全风险，确认没有问题后建议禁用。打开“本地用户和组”，双击Administrator，在账户已禁用前打勾。

## 3、删除文件资源管理器中左侧的OneDrive图标

如图：

![img](./assets/windows11偏好配置/0d07b01abd6d4533a66d0079e311a723.png)

删除方法如下（卸载OneDrive并不能删除该图标）：

win + R 输入regedit 打开注册表编辑器

点开计算机\HKEY_CLASSES_ROOT\CLSID ，Ctrl + F ， 输入你要删除的onedrive图标名称 eg”现代科技“   如删除默认图标，可搜索”onedrive“。

![img](./assets/windows11偏好配置/4b7ab397e5f0479d8478d087964bb9be.png)

双击打开第三个文件，将数值改为0

![img](./assets/windows11偏好配置/eccb045aa66f4f1c81dcafc5fb18e476.png)

Ctrl + shift +esc   打开任务管理器 右击Windows资源管理器 右击重启  即可

 ![img](./assets/windows11偏好配置/e880d706259f429e9c5a0109c2515f14.png)

再次打开文件资源管理器，更改数值的图标已经被删除。

