＃$（保险丝故障排除指南）

##一般提示

###升级保险丝
- 自上次建立项目后升级Fuse时，在构建之前清理项目（在项目文件夹的终端中运行“uno clean”）。

##日志
当调查问题时，检查由Fuse和编辑器插件编写的日志通常是有用的。这些可以在：

-  OS X：`〜/ .fuse / logs`
-  Windows：`％localappdata％\ Fusetools \ Fuse \ logs`

##连接问题

###发生网络错误

您会收到一条消息说`fuse：发生网络错误：无法解析主机<您的主机名>'请检查您的网络设置，然后重试。

####解决方案

您的网络存在问题。尝试在终端中执行`ping $（hostname）`。如果您收到错误讯息，则必须先修正网络设置，然后再继续。

### FailedToConnectToDaemon

你得到一个堆栈跟踪与`FailedToConnectToDaemon`在其中的某个地方
 
####解决方案

- 首先，尝试停止Fuse守护程序。
 -  Windows：右键单击托盘中的保险丝图标，单击“退出”
 -  OS X：按住Control键并点击菜单栏中的保险丝图标，单击“退出”
- 然后，新的守护进程将在下次与Fuse交互时自动启动。
- 如果没有帮助，请尝试注销并重新回到Windows / OS X.
- 如果没有帮助，请尝试重新启动计算机。

###预览 - 无法连接

在iOS或Android设备上预览时，您会收到“无法连接”的消息

####解决方案
- 确保您的设备已启用WiFi
- 确保您的设备已连接到与运行Fuse的计算机相同的WiFi 
- 如果您的计算机正在运行防火墙（例如Windows防火墙），请确保允许Fuse接受传入连接
- 如果您在Android设备上启用了共享（通过USB共享移动网络），请尝试停用该功能
- 如果仍有问题，请从纸盘/菜单栏图标中退出保险丝，然后重新启动预览

##无法预览或导出到Android

###症状
-  Android构建陷入“尝试卸载现有版本的APK”
- 构建完成与“错误：没有找到Android设备。

####解决方案
- 检查设备是否使用USB电缆连接
- 检查您的设备上是否启用了usb调试。这可能会因不同的设备和操作系统版本而有所不同，因此您可能必须搜索设备的特定说明，但第二点[here]（http://developer.android.com/tools/device.html#setting- up）是一个好的开始的地方。
- 检查您的设备是否显示在“adb devices”中。`adb`通常可以在
 - `C：\ ProgramData \ Uno \ SDKs \ AndroidSDK \ platform-tools`（Windows）
 - `/ usr / share / uno / SDKs / AndroidSDK / platform-tools`（OS X）
- 如果你在Windows上，你应该检查你有最新的USB驱动程序为您的设备。你可以阅读更多关于这里[这里]（http://developer.android.com/tools/extras/oem-usb.html）

##无法为iOS构建

###症状
-  iOS构建停在“代码签名错误：没有找到代码签名身份：没有找到有效的签名身份（即证书和私钥对）。

####解决方案
- 启动Xcode
- 创建一个新的，空白的iOS项目，并尝试构建它
- 当发现与“`没有配置文件匹配的应用程序签名身份”的错误对话时，出现````点击`修复问题'并让向导完成
- 关闭Xcode
- 现在从Fuse重新运行你的构建/预览

## Sublime插件不工作

###症状

- 您收到消息“加载时出错：Packages / Fuse / UX.tmLanguage”
- 您在Sublime中不能获得语法高亮

####解决方案

- 打开仪表板，单击“升级文本设置”，然后按照向导。
- 如果这不起作用，删除在`％APPDATA％\ Sublime Text 3 \ Installed Packages`（Windows）或`〜/ Library / Application Support / Sublime Text 3 / Installed Packages`（OS）中用`Fuse` X），然后再次尝试向导。

##我的Uno代码不在预览中运行/更新

###症状

- 对您的Uno代码的更改不会反映在预览中
- 您的Uno代码不会在预览中执行

####说明

- 与ux / js不同，当您保存和自动刷新预览时，Uno代码不会刷新，您必须重新启动Fuse。更好的解决方案是即将到来。
 -  Windows：右键单击托盘中的保险丝图标，单击“退出”
 -  OS X：按住Control键并点击菜单栏中的保险丝图标，单击“退出”
- 此外，你在`ux.uno`文件中的任何Uno代码都不会在预览中运行。
- 总而言之，通过“uno build”比通过预览更容易使用Uno代码。更好的解决方案是即将到来。

##预览说“糟糕！这里出了点问题”

预览应用程式时，出现「糟糕！出现问题」画面。

####解决方案

- 打开保险丝[Monitor]（https://www.fusetools.com/learn/guides/preview-and-export-monitor）查看有关问题的详细信息。

##本地预览不会在Windows上启动

如果控制台输出包含“GL_VERSION：1.1.0”和“GL_RENDERER：GDI Generic”，则问题很可能丢失/过期的OpenGL驱动程序。升级到最新的图形适配器驱动程序，然后重试。

此问题也可以由Windows 10下的驱动程序问题触发，特定于Intel HD Graphics 2000/3000/4000图形适配器。在这种情况下，你将无法使用即时更新进行本地预览，但您仍然可以通过执行常规构建在PC上测试您的应用程序：`fuse build -t = dotnetexe --run`

您当然还可以使用[在Android和iOS设备上预览]（https://www.fusetools.com/learn/guides/preview-and-export-device-preview）
