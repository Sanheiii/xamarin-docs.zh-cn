---
title: Xamarin.Forms 双屏
description: 本指南介绍了如何构建适用于双屏设备的 Xamarin.Forms 应用。
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1d2a443a6005050c518e21e4e0f2df64c2aab0c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562621"
---
# <a name="no-locxamarinforms-dual-screen"></a>Xamarin.Forms 双屏

Microsoft Surface Duo 等双屏设备有助于实现新的应用程序用户体验。 Xamarin.Forms 包含 `TwoPaneView` 和 `DualScreenInfo` 类，以便你可为这些双屏设备开发应用。

## <a name="get-started"></a>入门

请按照以下步骤将双屏功能添加到 Xamarin.Forms 应用：

1. 为解决方案打开“NuGet 包管理器”对话框。
2. 在“浏览”选项卡中，搜索 `Xamarin.Forms.DualScreen`。
3. 将 `Xamarin.Forms.DualScreen` 包安装到你的解决方案中。
4. 在 `OnCreate` 事件中，将以下初始化方法调用添加到 Android 项目的 `MainActivity` 类：

    ```csharp
    Xamarin.Forms.DualScreen.DualScreenService.Init(this);
    ```

    必须采用此方法，应用才能检测到应用状态中的更改（例如横跨两个屏幕的更改）。

5. 更新 Android 项目的 `MainActivity` 类上的 `Activity` 属性，使其包括所有的 `ConfigurationChanges` 选项：

    ```@csharp
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation
        | ConfigChanges.ScreenLayout | ConfigChanges.SmallestScreenSize | ConfigChanges.UiMode
    ```

    必须有这些值才能更可靠地报告配置更改和范围状态。 默认情况下，只有两个值会添加到 Xamarin.Forms 项目中，因此请记住添加 rest 以获得可靠的双屏支持。

## <a name="troubleshooting"></a>疑难解答

如果 `DualScreenInfo` 类或 `TwoPaneView` 布局未按预期工作，请仔细检查此页面上的设置说明。 省略或错误地配置 `Init` 方法或 `ConfigurationChanges` 属性值是导致错误的常见原因。

查看 [Xamarin.Forms 双屏示例](/dual-screen/xamarin/samples)，了解更多指南和参考实现。

## <a name="next-steps"></a>后续步骤

添加 NuGet 后，请按照以下指南将双屏功能添加到应用中：

- [双屏设计模式](design-patterns.md) - 在考虑如何以最佳方式在双屏设备上使用多个屏幕时，请参考该模式指南，查找最适合你的应用程序界面的设置。
- [TwoPaneView 布局](twopaneview.md) - Xamarin.Forms `TwoPaneView` 类是一种针对双屏设备进行优化的跨平台布局，设计灵感源自 UWP 控件。
- [DualScreenInfo 帮助程序类](dual-screen-info.md) - `DualScreenInfo` 类可用于确定你的视图位于哪个窗格中、视图的大小如何、设备的放置方向如何，以及铰链的角度等等。
- [双屏触发器](triggers.md) -[`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) 命名空间包含两个状态触发器，它们在附加布局的视图模式或窗口发生改变时触发 [`VisualState`](xref:Xamarin.Forms.VisualState) 更改。

有关详细信息，请参阅[双屏开发人员文档](/dual-screen/)。