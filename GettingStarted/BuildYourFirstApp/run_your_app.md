# 运行应用
如果你已经看过了上一节创建了一个Android工程,这个工程里面包含了一些默认的源文件,你可以立刻运行这个应用了.

你能怎样运行起应用依赖于两件事:是否已经有Android设备和是否你正在使用Android studio.本课程将会展示通过Android studio 或者命令行怎样在Android设备和Android模拟器上运行应用.

## 在真机上运行
这里将展示怎样在Android设备上安装和运行应用.
###  设置设备
 1. 用usb线把设备插连到开发电脑上.如果你使用的是windows,你可以需要安装和设备匹配的usb驱动.有关安装驱动的帮助,
 请看[OEM Usb驱动](http://developer.android.com/tools/extras/oem-usb.html)文档.
 2. 开启设备的usb调试功能. 在Android 4.0或者更新版本,去**设置** > **开发者选项**中选择.
 > 注意:在Android4.2或者更新版本,开发者选项默认是隐藏的,要让他可选需要到 设置 > 关于手机 选项中连续点击版本号7次,返回到之前的目录后就会看到开发者选项.

### 在Android studio中运行应用
  1. 选中工程文件中的一个文件,然后点击工具栏中的Run.
  2. 在之后显示出来的选择设备对话框中,选中" Choose a running device"单选框,选择你的设备,点击OK.
  Android studio就会在你连接的手机上安装这个应用并且启动它.

### 在命令行中运行应用
打开命令行然后导航到工程目录的根目录下,使用gradle在debug模式下构建项目,使用gradle封装的脚本,调用assembleDebug构建任务.
这样就在build/模块目录下生成了叫app-debug.apk的debug版apk.
On Windows platforms, type this command:
在Windows平台输入命令:
  gradlew.bat assembleDebug

在 macos平台输入命令:
  $ chmod +x gradlew
  $ ./gradlew assembleDebug

在构建项目完成之后,输出的apk文件存储在app/build/outputs/apk/ 目录下.
> 注解: 第一个命令(chomod)是给gradle脚本添加可执行权限,这个命令只是在第一次构建这个项目时候需要调用.

确认Android sdk platforms/ 目录已经加入到PATH环境变量中,然后执行:
$ adb install app/build/outputs/apk/app-debug.apk
在设备上找到MyFirstApp,然后打开它.

这就是在设备上构建和运行应用的过程,想要开始开发请继续下一个课程.

## 在模拟器上运行
无论是使用Android studio 还是命令行,想要在模拟器上运行应用都需要创建一个Android虚拟设备(AVD).
AVD是一个允许你创建一个特定的设备的模拟器设备配置.

### 创建一个AVD
 1.启动AVD Manager
    在Android studio中,选择 工具 > Android > AVD Manager,或者直接点击工具栏中的AVD Manager图标.调出AVD Manager的控制面板.
    或者在命令行,切换到sdk/目录下 执行:
     tools/android avd
  注释:从命令行里运行AVD Manager和在Android studio中调起AVD是不同的,所以下面的指导可能不是全部情况都适用的.
 2.在AVD Manager主面板中,点击Create Virtual Device(创建虚拟设备).
 3.在硬件选择窗口里,选择一种设备配置,例如 Nexus 6, 然后点击下一步.
 4.为AVD选择一个你想要的系统版本,点击下一步.
 5.核对配置的设置,点击完成.
 想了解更多关于使用AVD的内容,请查看用AVD Manager管理Android虚拟设备(http://developer.android.com/tools/devices/managing-avds.html).

### 在Android  studio上运行应用
  1.选择你的工程,然后点击工具栏的Run按钮.
  2.在选择设备窗口中,点击Launch emulator单选框.
  3.从虚拟设备下拉菜单中,选择你创建的模拟器,点击OK.

  模拟器加载它自己本身可能需要花费几分钟.然后你需要解锁屏幕,这时你的应用会在模拟器屏幕上出现.
###在命令行里运行应用
  1.在命令行里构建工程.工程模块构建完成输出的apk在 app/build/outputs/apk/目录下.
  2.确认Android sdk platforms/ 目录已经加入到PATH环境变量中.
  3.执行命令:
    $ adb install app/build/outputs/apk/apk-debug.apk
  4.在模拟器上找到MyFirstApp,然后打开它.

  这就是在模拟器上构建和运行应用的过程, 想要开始开发请继续下一个课程.