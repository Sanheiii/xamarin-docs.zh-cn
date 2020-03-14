---
title: 在哪里可以找到我的版本信息和日志？
description: 本文档介绍查找 Xamarin 版本信息和日志的位置。 诊断问题、提交 bug 或获取支持时，此信息很有用。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 68de58f499788d803aa0af6c68f20e2265b1d6b5
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305821"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>在哪里可以找到我的版本信息和日志？

## <a name="outline"></a>外边框

- [版本信息](#version-information)
  - Windows 版本信息
  - Mac 版本信息
  - Android SDK Tools，平台工具，生成工具
- [IDE 和安装程序日志](#ide-and-installer-logs)
  - [Windows 日志](#windows-logs)
    - Xamarin Studio
    - 面向 Visual Studio 的 Xamarin
    - Xamarin 通用安装程序
    - 单个 `.msi` 安装程序，详细日志
    - Visual Studio 启动，详细日志
  - [Mac 日志](#mac-logs)
    - 生成主机
  - Visual Studio for Mac
    - Xamarin Studio
    - Xamarin 安装程序
- [详细生成输出](#verbose-build-output-logs)
- [适用于 Xamarin 和 Xamarin iOS 应用的调试日志](#debug-logs-for-xamarin-apps)
  - Android `adb` logcat 日志
  - iOS 模拟器日志（在 Mac 上）
  - iOS 设备日志（在 Mac 上）

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />版本信息

通常最好将 "**复制信息**" 按钮中的所有信息发送回。 否则，我们通常需要请求其他信息。 例如，在帮助解决问题时，操作系统版本、Xcode 版本、安装的 Android API 级别和 .NET 版本都非常重要。

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Windows 版本信息

#### <a name="xamarin-studio"></a>Xamarin Studio

**有关 > 显示详细信息 > 复制信息的帮助 > [button]**

#### <a name="visual-studio"></a>Visual Studio

**有关 Microsoft Visual Studio > 复制信息的帮助 > [button]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Mac 版本信息

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**Visual Studio > 关于 Visual Studio > 显示详细信息 > 复制信息 [button]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools，平台工具，生成工具

打开 Android SDK 管理器，然后获取顶部**工具**部分的屏幕截图。

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

“工具”>“打开 Android SDK 管理器”

#### <a name="visual-studio"></a>Visual Studio

“工具”>“Android”>“打开 Android SDK 管理器...”

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />IDE 和安装程序日志

对于每个日志位置，请确保压缩并附加整个日志文件夹。

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Windows 日志

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a>Xamarin 的 <a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[如何获取 Visual Studio 安装日志](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Xamarin "通用" 安装程序

`%LOCALAPPDATA%\Xamarin\Universal`

这些是 `XamarinInstaller.exe` 安装程序中的日志。

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />单个 `.msi` 安装程序，详细日志

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

参考：[命令行选项](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Visual Studio 启动，详细日志

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

参考： [/log （devenv）](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Mac 日志

可以在 "查找器" 中选择 "前往" > "移**到文件夹**" 菜单项，然后将任何这些路径复制并粘贴到对话框中。

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio for Mac

`~/Library/Logs/VisualStudio/7.0` （此数字可能因您使用的版本而异）

此文件夹还可以通过 "帮助-> 打开日志目录" 打开。

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` （此数字可能因您使用的版本而异）

此文件夹还可以通过 "帮助-> 打开日志目录" 打开。

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Xamarin "通用" 安装程序

`~/Library/Logs/XamarinInstaller/Universal`

这些是 `XamarinInstaller.dmg` 安装程序中的日志。

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Xamarin 生成主机

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />详细生成输出

1. 启用[诊断 MSBuild 输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。

2. 对于 iOS 应用，还可以通过在 "项目属性" 下添加 `-v -v -v -v` 来启用**详细的 mtouch 输出** **> IOS Build > 常规（选项卡） > 其他选项 > 附加的参数**。

3. 清除并重新生成项目。

4. 将 IDE 的生成输出复制并粘贴到文本文件中。
     - Visual Studio （Windows）：**查看 > 输出 > 显示以下输出：生成**
     - Visual Studio for Mac： **View > pad > 错误 > 生成输出（选项卡）**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a>适用于 Xamarin 和 Xamarin iOS 应用的 <a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />调试日志

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**查看 > Pad > 应用程序输出**

（请注意，此菜单项仅在应用程序启动后才会显示。）

### <a name="visual-studio"></a>Visual Studio

**查看 > 输出 > 显示输出：调试**

### <a name="a-idadb-logcat-nameadb-logcat-android-adb-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [`adb`](https://developer.android.com/tools/help/adb.html) logcat 日志

运行 `adb` 命令后，从桌面附加 android_logcat 的文件 **。** 这些说明假定你只连接了一个设备。

另请参阅 " [Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)" 页。

#### <a name="visual-studio"></a>Visual Studio

1. **Tools > Android > 启动 Android Adb 命令提示符**
2. 清理日志： `adb logcat -c`
3. 重现此问题。
4. 输出日志： `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. **工具 > 打开 Android SDK 命令提示符**
2. 清理日志： `adb logcat -c`
3. 重现此问题。
4. 输出日志： `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />iOS 模拟器日志（在 Mac 上）

- 若要访问系统日志，请选择 "调试" > 在 iOS 模拟器应用中**打开 "系统日志 ...** "。

- 若要从模拟器查看故障报告，请打开 "Console" 并导航到 `~/Library/Logs > DiagnosticReports`。

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />iOS 设备日志（在 Mac 上）

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**查看 iOS 设备日志 > 的 > 填充**

#### <a name="xcode"></a>Xcode

**Windows > 设备 > $ {DeviceName}**

故障报告在 "**查看设备日志**" 按钮下提供。 设备的系统日志显示在窗口底部的 "泄漏" 箭头下。

#### <a name="xcode-5"></a>Xcode 5

**Windows > 组织程序 > 设备（选项卡） > $ {DeviceName}**
