---
title: 用 Xamarin 构建的 tvOS 应用疑难解答
description: 本文提供了一些提示，可帮助在使用 Xamarin 生成的 tvOS 应用程序的开发过程中进行故障排除。 它介绍了已知问题和具体的错误。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: db630ba7db1532f24fb6e810653427d1678ad5e2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434640"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>用 Xamarin 构建的 tvOS 应用疑难解答

_本文介绍了在使用 Xamarin 的 tvOS 支持时可能遇到的问题。_

<a name="Known-Issues"></a>

## <a name="known-issues"></a>已知问题

Xamarin 的 tvOS 支持的当前版本具有以下已知问题：

- **Mono Framework** –单声道 4.3 ProtectedData 无法解密 mono 4.2 中的数据。 因此， `Data unprotection failed` 当配置受保护的 NuGet 源时，NuGet 包将无法还原，并出现错误。
  - **解决方法** –在 Visual Studio for Mac 中，你将需要添加回使用密码身份验证的任何 NuGet 包源，然后再重新尝试还原包。
- **Visual Studio for Mac w/F # 外** 接程序–在 Windows 上创建 F # Android 模板时出错。 这仍应在 Mac 上正常运行。
- **Xamarin** –当目标框架设置为的情况下运行 Xamarin 和统一模板项目时 `Unsupported` ， `Could not connect to the debugger` 可能会出现弹出窗口。
  - **可能的解决方法** –降级稳定通道中可用的 Mono framework 版本。
- **Xamarin Visual studio & xamarin** –在 Visual Studio 中部署 WatchKit 应用程序时， `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` 可能会出现错误。

请在 [GitHub](https://github.com/xamarin/xamarin-macios/issues/new)上报告你发现的任何错误。

## <a name="troubleshooting"></a>疑难解答

以下部分列出了在将 tvOS 9 与 tvOS 结合使用时可能出现的一些已知问题，以及针对这些问题的解决方案：

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>可执行文件无效-可执行文件不包含 bitcode

尝试将 tvOS 应用提交到 Apple TV 应用商店时，可能会收到 _"无效可执行文件-可执行文件不包含 bitcode"_ 格式的错误消息。

若要解决此问题，请执行以下操作：

1. 在 Visual Studio for Mac 中，右键单击 **解决方案资源管理器** 中的 TvOS 项目文件，然后选择 " **选项**"。
2. 选择 " **TvOS 生成** "，并确保你在 " **发布** " 配置上： 

    [![选择 tvOS 生成选项](troubleshooting-images/ts01.png)](troubleshooting-images/ts01.png#lightbox)
3. 添加 `--bitcode=asmonly` 到 " **其他 mtouch 参数** " 字段，然后单击 **"确定"** 按钮。
4. 在 **发布** 配置中重新生成应用。

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>验证 tvOS 应用程序是否包含 Bitcode

若要验证 tvOS 应用版本是否包含 Bitcode，请打开终端应用并输入以下内容：

```csharp
otool -l /path/to/your/tv.app/tv
```

在输出中，查找以下内容：

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` 和 `size` 将有所不同，但其他字段应相同。

你将需要确保你使用的任何第三方静态 (`.a`) 库是针对 tvOS 库构建的， (不是 iOS 库) ，并且还包括 bitcode 信息。

对于包含有效 bitcode 的应用或库， `size` 将大于1。 在某些情况下，库可以具有 bitcode 标记，但不包含有效的 bitcode。 例如：

**无效的 Bitcode**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**有效的 Bitcode** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

请注意 `size` 上面列出的示例中两个库之间的差异。 必须从启用了 bitcode (Xcode 设置 `ENABLE_BITCODE`) 作为此大小问题的解决方案的 Xcode 存档版本生成库。

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>仅包含 arm64 切片的应用还必须在信息 info.plist 中的 UIRequiredDeviceCapabilities 列表中包含 "arm64"。

将应用提交到 Apple TV App Store 进行发布时，可能会出现以下格式的错误：

_"仅包含 arm64 切片的应用还必须在 info.plist 的 UIRequiredDeviceCapabilities 列表中包含" arm64 "_

如果出现这种情况，请编辑 `Info.plist` 文件，并确保其具有以下键：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
  <string>arm64</string>
</array>
```

重新编译你的应用程序以进行发布，并重新提交到 iTunes Connect。

### <a name="task-mtouch-execution----failed"></a>任务 "MTouch" 执行失败

如果你使用的是第三方库 (例如 MonoGame) ，并且你的发布编译失败，并在结束后出现一系列长错误消息 `Task "MTouch" execution -- FAILED` ，请尝试添加 `-gcc_flags="-framework OpenAL"` 到 **其他 touch 参数**：

[![任务 MTouch 执行](troubleshooting-images/mtouch01.png)](troubleshooting-images/mtouch01.png#lightbox)

还应 `--bitcode=asmonly` 在 **附加的触控参数**中包含，将链接器选项设置为 " **全部链接** "，并执行干净的编译。

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS-90471 错误。 缺少大图标

如果收到消息，格式为 "ITMS-90471 error"。 尝试将 tvOS 应用提交到 Apple TV 应用商店进行发布时，缺少大图标 "，请检查以下各项：

1. 确保已在文件中包含 `Assets.car` 使用 [应用图标](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) 文档创建的大图标资产。
2. 确保在 `Assets.car` 最终的应用程序捆绑包中包括了使用 [图标和图像](~/ios/tvos/app-fundamentals/icons-images.md) 的文档。

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>无效的捆绑–支持游戏控制器的应用还必须支持 Apple TV 远程

或 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>无效的捆绑包–带有 GameController 框架的 Apple 电视应用程序的信息中必须包括 GCSupportedGameControllers 键。 info.plist

游戏控制器可用于增强游戏，并在游戏中提供浸入式。 它们还可用于控制标准 Apple TV 界面，因此用户无需在远程和控制器之间切换。

如果你要将具有游戏控制器支持的 Xamarin tvOS 应用提交到 Apple TV 应用商店，并且你将收到如下所示的错误消息：

_我们发现你最近的 "应用名称" 传递有一个或多个问题。你的交付已成功完成，但你可能希望在下一次传递时更正以下问题：_

_无效的捆绑–支持游戏控制器的应用也必须支持 Apple TV 远程。_

或 

_无效的捆绑包–具有 GameController 框架的 Apple TV 应用必须在应用的 info.plist 中包含 GCSupportedGameControllers 键。_

解决方法是将对 Siri 远程 () 的支持添加 `GCMicroGamepad` 到应用 `Info.plist` 文件。 Apple 添加了微型游戏控制器配置文件以面向 Siri 远程。 例如，包括以下项：

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> 蓝牙游戏控制器是最终用户可能会进行的可选购买，你的应用程序无法强制用户购买一个。 如果你的应用程序支持游戏控制器，则它还必须支持 Siri 远程，以便所有 Apple TV 用户都可以使用该游戏。

有关详细信息，请参阅我们的[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档中的使用[游戏控制器](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers)部分。

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>目标框架不兼容：。NetPortable，Version = 5v，Profile = Profile78

尝试在 tvOS 项目中包含可移植类库 (PCL) 时，可能会在中看到一条消息：

_目标框架不兼容：。NetPortable，Version = 5v，Profile = Profile78_

若要解决此问题，请添加一个名为的 XML 文件， `Xamarin.TVOS.xml` 其中包含以下内容：

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

到以下路径：

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

请注意，路径中的配置文件编号必须与 PCL 的配置文件编号匹配。

准备好此文件后，应该能够成功地将 PCL 文件添加到 tvOS 项目。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)