---
title: Xamarin 中的 iPad 多任务
description: iOS 9 支持同时运行两个应用，同时使用 "滑动" 或 "拆分" 视图。 它还支持视频播放图片。
ms.prod: xamarin
ms.assetid: 0F2266D7-21FF-404D-A148-0CFDE76B12AA
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: e32042c4b7e7d67e219611014d3a06ad9ed17b9d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432466"
---
# <a name="multitasking-for-ipad-in-xamarinios"></a>Xamarin 中的 iPad 多任务

_iOS 9 支持同时运行两个应用，同时使用 "滑动" 或 "拆分" 视图。它还支持视频播放图片。_

![拆分屏幕示例](multitasking-images/about02-sml.png) ![图片中示例](multitasking-images/about03-sml.png)

iOS 9 增加了对在特定 iPad 硬件上同时运行两个应用程序的多任务支持。 通过以下功能支持 iPad 的多任务：

- [**滑过**](#Slide-Over) -允许用户在幻灯片外面板中临时运行第二个 iOS 应用 (在屏幕的右侧或左侧) ，其内容约为当前正在运行的主要应用程序的25%。 滑过只能在 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4上使用。
- [**拆分视图**](#Split-View) -在受支持的 ipad 硬件 (ipad Air 2 上，ipad 迷你4和 iPad Pro 仅) ，用户可以选择第二个应用并在拆分屏幕模式下并行运行与当前运行的应用程序。 用户可以控制每个应用占用的主屏幕的百分比。
- [**图片：对于**](#Picture-in-Picture) 播放视频内容的应用，视频现在可以在可移动且可调整大小的窗口中播放，该窗口将浮动在当前运行在 iOS 设备上的其他应用。 用户可以完全控制此窗口的大小和位置。 图片中的图片仅在 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4上可用。

[在应用程序中支持多任务](#Supporting-Multitasking-in-your-App)处理时，需要考虑以下事项：

- [屏幕大小和方向](#Screen-Size-Considerations)
- [自定义硬件键盘快捷方式](#Custom-Hardware-Keyboard-Shortcuts)
- [资源管理](#Resource-Management-Considerations)

作为应用开发人员，你还可以 [选择不执行多任务](#Opting-Out-of-Multitasking)，包括 [禁用 PIP 视频播放](#Disabling-PIP-Video-Playback)。

本文介绍必要的步骤，确保你的 Xamarin iOS 应用在多任务环境中正常运行，或者如果不适合你的应用，则如何选择不执行多任务。

> [!VIDEO https://youtube.com/embed/GctYAozoLr8]

**IPad 视频的多任务**

<a name="Multitasking-QuickStart"></a>

## <a name="multitasking-quickstart"></a>多任务快速入门

若要支持 **滑动** 或 **拆分视图** ，应用必须执行以下操作：

- 在 iOS 9 (或更高版本) 上构建。
- 使用情节提要启动屏幕 (，而不) 图像资产。
- 为 UI 使用带有自动布局和大小类的情节提要。
- 支持所有4个 iOS 设备方向 (纵向、倒置纵向、横向 & 横向右) 。

<a name="Multitasking"></a>

## <a name="about-multitasking-for-ipad"></a>关于 iPad 的多任务

iOS 9 在 iPad 上提供了新的多任务功能，同时引入了_滑动__视图_ (iPad Air 2、iPad 迷你4和 iPad Pro，仅) _图片_和图片。 以下部分将详细介绍这些功能。

<a name="Slide-Over"></a>

### <a name="slide-over"></a>滑过

使用滑动功能，用户可以选择第二个应用并将其显示在小型滑动面板中，以提供快速交互。 滑动面板是临时性的，当用户再次使用主应用时将关闭。

[![滑动面板](multitasking-images/about01.png)](multitasking-images/about01.png#lightbox)

需要注意的主要问题是，用户决定两个应用并行运行，开发人员不能控制此过程。 因此，需要执行一些操作来确保你的 Xamarin iOS 应用在面板上可正确运行：

- **使用 "自动** 布局" 和 "大小" 类-由于你的 Xamarin iOS 应用现在可以在滑出侧面板中运行，因此你不能再依赖设备、屏幕大小或其方向来布局你的 UI。 若要确保应用程序能够正确地缩放其接口，需要使用自动布局和大小类。 有关详细信息，请参阅我们的 [统一情节提要文档简介](~/ios/user-interface/storyboards/unified-storyboards.md) 。
- **有效使用资源** -由于应用现在可以与其他正在运行的应用共享系统，因此你的应用程序必须有效地使用系统资源。 当内存变得稀疏时，系统会自动终止消耗最多内存的应用程序。 有关更多详细信息，请参阅 Apple 的 [适用于 IOS 应用的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) 。

滑过只能在 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4上使用。 若要详细了解如何准备应用程序以进行滑过，请参阅 Apple [在 iPad 文档上采用多任务增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) 。

<a name="Split-View"></a>

### <a name="split-view"></a>拆分视图

在支持的 iPad 硬件 (iPad Air 2 上，iPad 迷你4和 iPad Pro 仅) ，用户可以选择第二个应用，并在拆分屏幕模式下并行运行与当前运行的应用程序。 用户可以通过拖动屏幕分隔线来控制每个应用占有的主屏幕的百分比。

[![拆分视图](multitasking-images/about02.png)](multitasking-images/about02.png#lightbox)

如滑过，用户将决定两个应用并行运行，开发人员不能控制这一过程。 因此，"拆分" 视图对 Xamarin iOS 应用程序的要求类似于：

- **使用 "自动版式" 和 "大小" 类** -由于你的 Xamarin iOS 应用现在可以在用户指定大小的拆分屏幕模式下运行，因此你不能再依赖设备、屏幕大小或其方向来布局你的 UI。 若要确保应用程序能够正确地缩放其接口，需要使用自动布局和大小类。 有关详细信息，请参阅我们的 [统一情节提要文档简介](~/ios/user-interface/storyboards/unified-storyboards.md) 。
- **有效使用资源** -由于应用现在可以与其他正在运行的应用共享系统，因此你的应用程序必须有效地使用系统资源。 当内存变得稀疏时，系统会自动终止消耗最多内存的应用程序。 有关更多详细信息，请参阅 Apple 的 [适用于 IOS 应用的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) 。

若要详细了解如何准备应用程序以进行拆分视图，请参阅 [在 iPad 文档上 Apple 采用多任务增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145) 。

<a name="Picture-in-Picture"></a>

### <a name="picture-in-picture"></a>图片

图片中的新图片功能 (也称为 _PIP_) 使用户能够在一个小的浮动窗口中观看视频，用户可以在屏幕上的任何位置与其他正在运行的应用程序进行定位。

[![图片浮动窗口中的示例图片](multitasking-images/about03.png)](multitasking-images/about03.png#lightbox)

与 "滑动" 和 "拆分" 视图一样，用户可以完全控制在图片模式下观看视频。 如果你的应用程序的主要功能是观看视频，则需要进行一些修改才能在 PIP 模式下正常运行。 否则，不需要进行任何更改即可支持 PIP。

要使应用在用户请求中显示 PIP 视频，需要使用 _AVKit_ 或 _AV 基础 api_。 Media Player 框架已在 iOS 9 中进行了折旧，并且不支持 PIP。

图片中的图片仅在 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4上可用。 有关详细信息，请参阅 [PictureInPicture 示例应用](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9) 和 Apple 快速入门文档 [中的图片](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForPictureInPicture.html#//apple_ref/doc/uid/TP40015145-CH14) 。

<a name="Supporting-Multitasking-in-your-App"></a>

## <a name="supporting-multitasking-in-your-app"></a>在应用中支持多任务

对于任何现有的 Xamarin iOS 应用，只要你的应用已遵循 Apple 的设计指南和适用于 iOS 8 的最佳实践，支持多任务就是一种透明的任务。 这意味着应用应使用具有自动版式和大小类的情节提要来实现其用户界面布局 (请参阅我们的 [统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md) ，了解) 的详细信息。

对于这些应用程序，需要进行少量更改或无需更改即可支持多任务处理并在其内部运行。 如果应用的 UI 是使用其他方法（例如，在 c # 代码中直接定位和调整 UI 元素）创建的，或者如果它依赖于特定的设备屏幕大小或方向，则需要进行重大修改才能正确地支持 iOS 9 工作。

若要在任何新的 Xamarin 应用上支持 iOS 9 工作任务，请再次将演示图板与自动布局和大小类一起用于所有应用的用户界面布局，并实现以下部分中的说明。

<a name="Screen-Size-Considerations"></a>

### <a name="screen-size-and-orientation-considerations"></a>屏幕大小和方向注意事项

在 iOS 9 之前，你可以针对特定的设备屏幕大小和方向设计你的应用程序。 由于应用现在可以在 "滑出" 面板或 "拆分" 视图模式下运行，因此它可以在 iPad 上的紧凑或普通水平大小类中运行，而不考虑设备的物理方向或屏幕大小。

[![屏幕大小和方向注意事项](multitasking-images/sizeclasses01.png)](multitasking-images/sizeclasses01.png#lightbox)

在 iPad 上，全屏应用具有常规的水平和垂直大小类。 除了 iPhone 6 Plus 和 iPhone 6s Plus 外，所有 Iphone 都在任意方向上都具有紧凑大小的类。 在横向模式下，iPhone 6 Plus 和 iPhone 6s Plus 具有常规的水平大小类和紧凑的垂直大小类 (非常类似于 iPad 微型) 。

在支持 "滑动" 和 "拆分" 视图的 Ipad 上，最终可以得到以下组合：

| **方向** | **主要应用** | **辅助应用** |
|--- |--- |--- |
| **纵向** |75% 的屏幕<br />水平压缩<br />常规垂直|25% 的屏幕<br />水平压缩<br />常规垂直|
| **横向** |75% 的屏幕<br />常规水平<br />常规垂直|25% 的屏幕<br />水平压缩<br />常规垂直|
| **横向** |50% 的屏幕<br />水平压缩<br />常规垂直|50% 的屏幕<br />水平压缩<br />常规垂直|

在示例 [MuliTask](/samples/xamarin/ios-samples/ios9-multitask) 应用中，如果它在 "横向" 模式下的 iPad 上以全屏方式运行，则会同时显示列表和详细信息视图：

[![同时显示的列表和详细信息视图](multitasking-images/sizeclasses03.png)](multitasking-images/sizeclasses03.png#lightbox)

如果在幻灯片上方的幻灯片中运行同一应用程序，则该应用程序将布局为精简水平大小类并仅显示列表：

[![仅当设备为水平时显示的列表](multitasking-images/sizeclasses04.png)](multitasking-images/sizeclasses04.png#lightbox)

若要确保你的应用在这些情况下正常运行，你应采用特性集合以及大小类并符合 `IUIContentContainer` 和 `IUITraitEnvironment` 接口。 有关详细信息，请参阅 Apple 的 [UITraitCollection 类参考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202) 和 [统一的情节提要指南简介](~/ios/user-interface/storyboards/unified-storyboards.md) 。

此外，不能再依赖设备屏幕边界来定义应用的可见区域，而需要改用应用的窗口边界。 由于窗口边界完全受用户控制，无法以编程方式调整它们或防止用户更改这些边界。

最后，你的应用程序必须使用情节提要文件来显示其启动屏幕，而不是使用一组 **.png** 图像文件，并且支持所有四个界面方向 (纵向、倒置纵向、横向和横向右) ，以在幻灯片上方或拆分视图模式下运行。

<a name="Custom-Hardware-Keyboard-Shortcuts"></a>

### <a name="custom-hardware-keyboard-shortcuts"></a>自定义硬件键盘快捷方式

在 iPad 上运行的 iOS 9 中，Apple 提供对硬件键盘的扩展支持。 Ipad 始终包括通过蓝牙的基本外部键盘支持，某些键盘制造商创建了包含硬连线特定于 iOS 的密钥的键盘。

现在，在 iOS 9 中，应用程序可以创建自己的自定义键盘快捷方式。 此外，还提供了一些基本的键盘快捷方式，如 **命令-C** (复制) 、 **命令 X** (剪切) 、 **命令 V** (粘贴) 和 **命令移位-H** (home) ，而没有专门编写的应用对其进行响应。

**命令选项卡** 将显示一个应用切换器，使用户能够在应用程序之间快速切换，就像 Mac OS 一样：

[![应用切换器](multitasking-images/keyboard01.png)](multitasking-images/keyboard01.png#lightbox)

如果 iOS 9 应用包含键盘快捷方式，则用户可以按住 **命令**、 **选项** 或 **控制** 键在弹出窗口中显示它们：

[![键盘快捷方式弹出窗口](multitasking-images/keyboard02.png)](multitasking-images/keyboard02.png#lightbox)

#### <a name="defining-custom-keyboard-shortcuts"></a>定义自定义键盘快捷方式

如果我们将以下代码添加到应用中的视图或视图控制器，则当该视图或控制器可见时，将提供自定义键盘快捷方式：

```csharp
#region Custom Keyboard Shortcut
public override bool CanBecomeFirstResponder {
    get { return true; }
}

public override UIKeyCommand[] KeyCommands {
    get {

        var keyCommand = UIKeyCommand.Create (new NSString("n"), UIKeyModifierFlags.Command, new Selector ("NewEntry"), new NSString("New Entry"));
        return new UIKeyCommand[]{ keyCommand };
    }
}

[Export("NewEntry")]
public void NewEntry() {

    // Add a new entry
    ...

}
#endregion
```

首先，重写 `CanBecomeFirstResponder` 属性并返回， `true` 以便视图或视图控制器可以接收键盘输入。 

接下来，我们将重写 `KeyCommands` 属性，并 `UIKeyCommand` 为 **命令-N** 击键创建新的。 当启用击键时，我们将 `NewEntry` 使用命令) 向 iOS 9 公开 (方法， `Export` 以执行请求的操作。

如果在附加了硬件键盘的 iPad 上运行此应用，并且用户键入 **命令-N**，则会向列表中添加一个新条目。 如果用户按住 **命令** 键，将显示快捷方式的列表：

[![键盘快捷方式弹出窗口](multitasking-images/keyboard03.png)](multitasking-images/keyboard03.png#lightbox)

有关示例实现，请参阅示例工作时 [应用](/samples/xamarin/ios-samples/ios9-multitask) 。

<a name="Resource-Management-Considerations"></a>

### <a name="resource-management-considerations"></a>资源管理注意事项

即使对于已使用 iOS 8 设计指南和最佳实践的应用程序，对资源进行高效管理仍可能是一个问题。 在 iOS 9 中，应用不再独占使用内存、CPU 或其他系统资源。

因此，你必须对 Xamarin iOS 应用进行微调，以便有效地使用系统资源，或者在内存不足的情况下将其视为终止。 这同样适用于选择不执行多任务的应用程序，因为第二个应用程序可能仍在幻灯片上方或图片窗口中运行，需要额外的资源或使刷新速度低于60帧/秒。

请考虑以下用户操作及其含义：

- **在面板上的幻灯片中输入文本** -即使您的应用程序没有文本输入，现在也可以通过其 UI 显示系统键盘。 因此，应用可能需要响应键盘显示通知 (例如显示和隐藏键盘) 。
- **在幻灯片上方的幻灯片中运行第二个应用** -新应用现在正在前台运行，并与现有应用争用系统资源（例如内存和 CPU 周期）。
- **在 PIP 窗口中播放视频** -不仅可以在此窗口中包含应用界面的一部分，而且启动视频的应用仍会在后台运行，并且消耗 CPU 和内存资源。

若要确保应用有效地使用资源，应执行以下操作：

- **使用乐器分析应用** 程序-检查内存泄漏、明显 CPU 使用情况以及应用可能阻塞主线程的区域。
- **响应状态转换方法** -在 **AppDelegate.cs** 文件中，重写并响应状态更改方法，如应用进入或返回到后台。 释放任何 unrequired 资产，如图像、数据或视图以及视图控制器。
- 并行**测试并行内存的应用**程序-在物理 iOS 硬件上，使用 "滑出" 和 "拆分" 视图在物理 iOS 硬件上使用 "向外扩展" 和 "拆分" 视图（例如 (在卫星视图模式下的地图）) 并测试这两个应用是否保持响应且不会崩溃。

有关资源管理的详细信息，请参阅 Apple 的 [适用于 IOS 应用的能源效率指南](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/EnergyGuide-iOS/index.html#//apple_ref/doc/uid/TP40015243) 。

<a name="Opting-Out-of-Multitasking"></a>

## <a name="opting-out-of-multitasking"></a>退出多任务

虽然 Apple 建议所有 iOS 9 应用程序都支持多任务处理，但对于应用程序而言，可能有非常具体的原因（例如需要全屏工作才能正常运行的游戏或照相机应用）。

如果你的 Xamarin iOS 应用程序选择不在 "滑出" 面板或 "拆分" 视图模式下运行，则编辑项目的 **info.plist** 文件并选中 " **需要全屏**"：

[![退出多任务](multitasking-images/fullscreen01.png)](multitasking-images/fullscreen01.png#lightbox)

> [!IMPORTANT]
> 尽管选择退出多任务会阻止你的应用程序在 "幻灯片 Out" 或 "拆分" 视图中运行，但它不会阻止其他应用程序在 "滑出" 或 "图片" 视频中运行，因为它与你的应用程序一起显示。

<a name="Disabling-PIP-Video-Playback"></a>

### <a name="disabling-pip-video-playback"></a>禁用 PIP 视频播放

在大多数情况下，你的应用程序应允许用户播放其在图片浮动窗口中显示的任何视频内容。 但是，在某些情况下可能不需要这样做，例如游戏切削场景视频。

若要选择退出 PIP 视频播放，请在应用中执行以下操作：

- 如果使用 `AVPlayerViewController` 来显示视频，请将 `AllowsPictureInPicturePlayback` 属性设置为 `false` 。
- 如果使用 `AVPlayerLayer` 显示视频，请不要实例化 `AVPictureInPictureController` 。
- 如果使用 `WKWebView` 来显示视频，请将 `AllowsPictureInPictureMediaPlayback` 属性设置为 `false` 。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了在 iOS 9 的新多任务功能中运行 Ipad 所需的步骤，以确保其正常运行。 此外，它还介绍了如何在应用程序的不太适合的情况下，对应用程序进行多任务处理。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [) 的 (示例 ](/samples/xamarin/ios-samples/ios9-multitask)
- [统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [在 iPad 上采用多任务增强功能](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/index.html#//apple_ref/doc/uid/TP40015145)
- [博客文章](https://blog.xamarin.com/using-auto-layouts-for-ios-9-splitview/)