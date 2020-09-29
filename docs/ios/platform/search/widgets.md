---
title: IOS 10 中的搜索和主屏幕小组件增强功能
description: 本文档介绍 Apple 对 iOS 10 中的小组件的增强功能，包括搜索和主屏幕小组件的更新。
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 9a45fcccd693a51230a51600964acd75704e1b93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429713"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>IOS 10 中的搜索和主屏幕小组件增强功能

_本文介绍 Apple 在 iOS 10 中对小组件系统的增强功能。_

Apple 为小组件系统引进了几项增强功能，以确保小组件在新的 iOS 10 锁屏上存在的任何背景上看起来很出色。 此外，小组件现在包含 [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) 属性，该属性允许开发人员描述可用内容的数量，并允许用户展开和折叠内容。

小组件 (也称为今日扩展) 是一种特殊类型的 iOS 扩展，可显示少量有用的信息或及时公开应用特定的功能。 例如，"新闻" 应用程序有一个小组件，其中显示了顶部标题，日历应用程序提供了两个不同的小组件：一个用于显示今天的事件，另一个用于显示即将发生的事件。

小组件可高度自定义，并且可能包含诸如文本、图像、按钮等的 UI 元素。此外，开发人员还可以进一步自定义小组件的布局。

[![示例小组件](widgets-images/widgets01.png)](widgets-images/widgets01.png#lightbox)

用户可以通过两个主要位置来查看应用的小组件并与之进行交互：

- **搜索屏幕** -用户可以在搜索屏幕中添加他们最有用的小组件。 通过在主页和锁定屏幕上向右轻扫访问搜索屏幕。
- **主屏幕** -在主屏幕中，用户可以通过对应用图标施加压力来使用3d 触摸来打开 "快速操作" 列表。 应用的小组件将显示在 "快速操作" 列表的上方。 有关详细信息，请参阅有关 [3D Touch](~/ios/platform/3d-touch.md) 文档的简介。

## <a name="widgets-developer-suggestions"></a>小组件开发人员建议

理想情况下，开发人员应始终尝试并设计用户要添加到其搜索屏幕的小组件。 为此，Apple 提供了以下建议：

- **创建极佳的 Glanceable 体验** -用户需要提供简短、Glanceable 的状态更新信息的小组件，或允许他们快速执行简单任务。 这使得提供了正确的信息量和交互性。 请尽可能允许用户使用一次点击来执行给定的任务。 此外，由于小组件不支持平移或滚动，因此必须在小组件设计中考虑这一点。
- **快速显示内容** -小组件设计为 glanceable，因此在显示小组件后，用户应始终等待内容加载。 小组件应在本地缓存其内容，以便在后台加载新内容时，它们可显示最新内容。
- **提供适当的填充和边距** -小组件看起来不会太拥挤，因此应避免将内容扩展到小组件视图的边缘。 边缘和内容之间应始终有多个像素宽的边距。 Apple 还建议使用应用程序图标，该图标显示在小组件顶部，作为对齐指南。 如果小组件显示网格布局，请确保网格中的项之间有适当的填充，并尝试将项数限制为四个最大值。
- **使用适应性布局** -小组件的宽度将根据其运行所在的设备和设备的方向而有所不同。 小组件的高度也可能随之显示在折叠 (中，但在默认) 或展开 (不受所有小组件) 状态的支持。 折叠小组件具有大约两个和半个标准 Io 表行的高度。 开发人员可以请求扩展小组件的大小，但理想情况下应小于屏幕的高度。 在折叠状态下，小组件只应显示必需的独立信息。 展开后，该小组件应显示补充信息以增强折叠状态中显示的主要内容。 "快速操作" 列表中显示的小组件只会处于折叠状态。
- **不要自定义小组件的背景** 小组件会显示在系统提供的浅亮度背景上。 这样做是为了提高小组件之间的一致性并提高其内容的易读性。 避免使用图像作为小组件背景，因为它可能与用户的锁定和主屏幕墙纸冲突。
- **以黑色或深灰色使用系统字体** -在小组件中显示文本时，系统字体的效果最佳。 该字体的颜色应为黑色或深灰色，以使其与明亮的小小小组件背景相对突出。
- 当适当小组件应始终与应用程序分开操作**时提供应用访问**，但是，如果需要更深层的功能，该小组件应该能够启动应用以查看或编辑特定的信息片段。 永远不要包含 "打开应用" 按钮，只需允许用户点击内容本身，而永远不会打开第三方应用。
- **选择清晰、简洁的小组件名称** -应用的图标和小组件的名称始终显示在小组件的内容上。 Apple 建议使用应用的名称作为其主要小组件，并为其提供的任何其他组件提供清晰、简洁的名称。 提供自定义小组件标题时，它们应以应用名称作为前缀 (例如地图附近、地图餐馆等 ) 。
- **身份验证添加值时发出通知** -如果附加功能或信息仅在用户进行身份验证和登录时才可用，则向用户显示此信息。 例如，"一项共享" 应用可能会说 "登录到书籍"。
- **选择快速操作列表小组件** -如果应用程序提供多个小组件，开发人员应选择在用户显示 "快速操作" 列表时要显示的小组件，方法是使用3d 触控对应用图标施加压力。

有关使用小组件的更多详细信息，请参阅我们的 [扩展简介](~/ios/platform/extensions.md)、 [3d 触摸文档简介](~/ios/platform/3d-touch.md) 和 Apple [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)。

## <a name="working-with-vibrancy"></a>使用活力

活力可确保小组件的文本在显示在小组件光线上时保持清晰，并且系统) 提供模糊背景 (。 在 iOS 10 之前，开发人员将为小组件的活力使用 [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) 。 例如：

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

IOS 10 中已弃用此项，应将其替换为 [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) 或 [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect)。 例如：

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>使用折叠和展开的小组件

IOS 10 中新增了小组件现在包含 [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) 属性，该属性允许开发人员描述可用内容以及允许用户展开和折叠内容。

首次显示小组件时，它处于折叠状态。 折叠小组件具有大约两个和半个标准 Io 表行的高度。 开发人员可以请求扩展小组件的大小，但理想情况下应小于屏幕的高度。

在折叠状态下，小组件只应显示必需的独立信息。 展开后，该小组件应显示补充信息以增强折叠状态中显示的主要内容。 例如，天气应用显示折叠的当前天气状况，并在展开时添加每小时预测。

"快速操作" 列表中显示的小组件只会处于折叠状态。 如果应用程序提供多个小组件，开发人员应选择要在用户通过使用三维触控向应用图标施加压力时显示的 "快速操作" 列表。

下面的示例是一个简单的当今扩展 (小组件) ，用于处理折叠和展开状态：

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

详细了解小组件显示模式特定代码。 若要告知系统此小组件支持扩展状态，请使用：

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

若要获取小组件的当前显示模式，请使用：

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

若要获取折叠或展开状态的最大大小，则使用：

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

为了处理) 更改 (显示模式状态，它使用：

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

除了将每个状态的请求大小设置 (折叠或展开) ，它还会更新要显示的内容以匹配新大小。

## <a name="summary"></a>总结

本文介绍了 Apple 在 iOS 10 中对小组件系统的增强功能，并演示了如何在 Xamarin 中实现这些功能。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [扩展简介](~/ios/platform/extensions.md)
- [3D Touch 简介](~/ios/platform/3d-touch.md)
- [应用扩展编程指南](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)