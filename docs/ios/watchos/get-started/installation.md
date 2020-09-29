---
title: 在 Xamarin 中安装和使用 watchOS
description: 本文档介绍如何在 Xamarin 中安装和使用 watchOS。 它讨论了安装、watchOS 项目结构、如何使用 iOS 设计器 Xcode 集成，并提供了故障排除提示。
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/05/2017
ms.openlocfilehash: 89d074e14f2a66d472f06acecf42127a7ff44c4c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437378"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>在 Xamarin 中安装和使用 watchOS

watchOS 4 需要 macOS Sierra (10.12) ，Xcode 9。

watchOS 1 最初需要 OS X Yosemite (10.10) ，Xcode 7。

> [!WARNING]
> [2018 年4月1日之后，将不会接受 watchOS 1 更新](https://developer.apple.com/news/?id=11162017a)。 将来的更新必须使用 watchOS 2 SDK 或更高版本;建议使用 watchOS 4 SDK 生成。

## <a name="project-structure"></a>项目结构

监视应用由三个项目组成：

- **Xamarin IOS iphone 应用项目** -这是一个普通的 iPhone 项目，它可以是任何 Xamarin iOS 模板。 监视应用及其扩展将捆绑在此主项目中。

- **监视扩展项目** -此项包含用于监视应用的代码 (例如控制器类) 。

- **监视应用项目** -此项包含用户界面情节提要文件，其中包含用于监视应用的所有 UI 资源。

在 Xamarin Studio 中， [监视工具包目录示例](/samples/xamarin/ios-samples/watchos-watchkitcatalog) 解决方案如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Visual Studio 中的解决方案](installation-images/catalog-solution.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Visual Studio 中的解决方案](installation-images/catalog-solution-vs.png)

-----

下载并运行 [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog) 示例以开始工作。
可以在 " [控件](~/ios/watchos/user-interface/index.md) " 页上找到该示例中的屏幕。

## <a name="creating-a-new-project"></a>创建新项目

不能创建新的 "监视解决方案" .。。你可以向现有的 iOS 应用程序添加监视应用。 按照以下步骤创建一个监视应用：

1. 如果没有现有项目，请先选择 " **文件" > "新建解决方案** " 并创建一个 iOS 应用 (例如，) 的 **单个视图应用** ：

    [![选择 "文件" > "新建解决方案" 并创建 iOS 应用](installation-images/cycle8-2-sml.png)](installation-images/cycle8-2.png#lightbox)

2. 创建 iOS 应用后 (或者计划使用现有 iOS 应用) ，右键单击该解决方案，然后选择 " **添加 > 添加新项目 ...**"在 " **新建项目** " 窗口中，选择 " **watchOS > 应用 > WatchKit 应用**：

    [![选择 watchOS > 应用 > WatchKit 应用](installation-images/cycle8-6-sml.png)](installation-images/cycle8-6.png#lightbox)

3. 下一屏幕中，你可以选择哪个 iOS 应用项目应包含 watch 应用：

    [![选择应包含 watch 应用的 iOS 应用项目](installation-images/cycle8-7-sml.png)](installation-images/cycle8-7.png#lightbox)

4. 最后，选择保存项目的位置 (并根据需要启用源代码管理) ：

    [![选择保存项目的位置](installation-images/cycle8-8-sml.png)](installation-images/cycle8-8.png#lightbox)

5. Visual Studio for Mac 自动为你配置 [项目引用和 **info.plist** 设置](~/ios/watchos/get-started/project-references.md) 。

## <a name="creating-the-watch-user-interface"></a>创建监视用户界面

<a name="designer"></a>

### <a name="using-the-xamarin-ios-designer"></a>使用 Xamarin iOS 设计器

双击 "监视" 应用的 **界面** ，使用 IOS 设计器进行编辑。 可以将界面控制器和 UI 控件从 **工具箱** 拖动到情节提要，并使用 " **属性** " 板配置它们：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![设计器中的情节提要](installation-images/iosdesigner-sml.png)](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![设计器中的情节提要](installation-images/iosdesigner-sml-vs.png)](installation-images/iosdesigner-vs.png#lightbox)

-----

你应通过选择一个类，然后在 "**属性**" pad 中输入名称，为每个新的接口控制器提供一个**类**， (这将自动创建所需的 c # 代码隐藏文件) ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![为每个新的接口控制器指定一个类](installation-images/iosdesigner-classname.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![为每个新的接口控制器指定一个类](installation-images/iosdesigner-classname-vs.png)

-----

通过按 **Ctrl 并** 将按钮、表或接口控制器拖动到另一个接口控制器来创建 segue。

### <a name="using-xcode-on-the-mac"></a>在 Mac 上使用 Xcode

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

右键单击 Xcode 文件并选择 "打开方式"，然后选择 " **打开方式" > Xcode Interface Builder**，可以继续使用生成用户界面：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Visual Studio 用户还可以通过切换到直接使用 Mac 生成主机来使用 Xcode 构建其用户界面。
在 Visual Studio for Mac 中打开解决方案，然后右键单击 "Xcode" 文件，然后选择 " **打开方式" > "Interface Builder**：

-----

![打开 Xcode 中的接口。情节提要 Interface Builder](installation-images/openwith-xcode.png)

如果使用的是 Xcode，则应遵循用于监视应用的相同步骤，就像使用普通的 [iOS 应用情节提要](~/ios/user-interface/storyboards/index.md) 一样 (例如，通过 **Ctrl + 拖动** 到 **.h** 头文件) 来创建输出口和操作。

在 Xcode 中保存情节提要后 Interface Builder 它会自动将所创建的插座和操作添加到 "监视扩展" 项目中的 **designer.cs** 文件。

### <a name="adding-additional-screens-in-xcode"></a>在 Xcode 中添加其他屏幕

如果添加的其他屏幕 (模板中包含的内容在默认情况下) 情节提要 Interface Builder，则必须为每个新的接口控制器 **手动添加 c # 代码文件** 。

请参阅 [有关如何向情节提要添加新的界面控制器的高级说明](~/ios/watchos/troubleshooting.md#add)。

*Xamarin iOS 设计器自动执行此操作，无需手动步骤。*

## <a name="building"></a>生成

包含 watch 应用的项目生成方式与其他 iOS 项目相同。 生成过程将生成一个 iPhone 应用程序 () ，其中包含一个监视扩展 ( appex) ，后者又包含无代码的监视应用程序 (。

## <a name="launching"></a>启动

可以使用 Visual Studio for Mac 或 Visual Studio (在 Mac 生成主机) 上启动，在模拟器中启动监视应用。

可通过两种模式启动 WatchKit 应用：

- 正常应用模式 (默认的) 和
- [通知](~/ios/watchos/platform/notifications.md) (要求) 使用 JSON 格式的测试通知有效负载。

### <a name="xcode-8-support"></a>Xcode 8 支持

安装了 Xcode 8 (或更高版本的) 后，Apple Watch 模拟器与 iOS 模拟器不同 (不同于 [Xcode 6](#xcode6)，它们显示为 *外部显示*) 。
选择 "监视应用项目" 并将其设为 "启动项目" 时，"模拟器" 列表将显示从 (中选择的 *IOS 模拟器*) ，如下所示。

[![选择模拟器类型](installation-images/xs-xcode8-watchos3-sml.png)](installation-images/xs-xcode8-watchos3.png#lightbox)

开始调试时，应启动 *两个* 模拟器-iOS 模拟器 *和* Apple Watch 模拟器。 使用 **Command + Shift + H** 导航到 "监视" 菜单和 "时钟面";并使用 **硬件** 菜单设置 **Force Touch 压力**。 滚动触控板或鼠标将使用 Digital Crown 进行模拟。

#### <a name="troubleshooting"></a>疑难解答

如果尝试启动到不具有配对监视的模拟器，则会在 **应用程序输出** 中显示以下错误：

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

如果默认值不起作用，请参阅 [Apple 的论坛](https://forums.developer.apple.com/thread/7783) 了解有关配置模拟器的说明。

<a name="xcode6"></a>

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 和 watchOS 1

在运行或调试应用之前，必须使 *监视扩展项目* 成为 **启动项目** 。 你无法 "启动" 监视应用本身，如果你选择 iOS 应用，则它将在 iOS 模拟器中正常启动。

默认情况下，监视应用以正常 **应用** 模式启动 (不会一目了然或通知模式) Visual Studio for Mac 的 " **运行** " 或 " **调试** " 命令。

当使用 Xcode 6 时，只有 iPhone 5、iPhone 5S、iPhone 6 和 iPhone 6 Plus 可以激活显示监视应用程序 **Apple Watch-38mm** 或 **Apple Watch 42mm** 的外部显示器。

> [!NOTE]
> 请记住，使用 Xcode 6 时，"监视" 屏幕不会自动出现在 iOS 模拟器中。
> 使用 " **硬件 > 外部显示** " 菜单来显示 "监视" 屏幕。

<a name="custommodes"></a>

## <a name="launching-notification-mode"></a>启动通知模式

有关如何在代码中处理通知的信息，请参阅 " [通知" 页](~/ios/watchos/platform/notifications.md) 。

Visual Studio for Mac 可以使用通知 _启动模式_ 为通知启动 watch 应用：

右键单击 "监视应用程序" 项目，然后选择 " **运行 > 自定义配置 ...**"：

[![运行自定义配置](installation-images/runwith-customparams-sml.png)](installation-images/runwith-customparams.png#lightbox)

这将打开 " **自定义参数** " 窗口，您可以在其中选择 **通知** (并提供 JSON 负载) ，然后按 " **运行** " 以在模拟器中启动 watch 应用：

[![设置通知和负载](installation-images/runwith-execargs-sml.png)](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>调试

Visual Studio for Mac 和 Visual Studio 都支持调试。
在通知模式下进行调试时，请记得提供通知 JSON 文件。 此屏幕截图显示在监视应用中命中的调试断点：

![此屏幕截图显示在监视应用中命中的调试断点](installation-images/debug-sml.png)

按照启动说明完成后，你将会看到在 IOS 模拟器上运行的监视应用 ** (监视) **。
对于通知模式，可以选择 " **调试" > 打开系统日志** (**CMD +/**) 并 `Console.WriteLine` 在代码中使用。

### <a name="debugging-lifecycle-event-handlers"></a>调试生命周期事件处理程序

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

WatchOS 模板文件 (如 `InterfaceController` 、 `ExtensionDelegate` 、 `NotificationController` 和 `ComplicationController`) 附带已实现的必需生命周期方法。 添加 `Console.WriteLine` 调用并读取 **应用程序输出** ，以更好地了解事件生命周期。

## <a name="related-links"></a>相关链接

- [WatchKitCatalog (示例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [第一次观看应用视频](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的 WatchKit 提示](https://developer.apple.com/watchkit/tips/)