---
title: 使用 Xamarin 中的 tvOS 按钮
description: 本文档介绍如何在使用 Xamarin 生成的 tvOS 应用中使用按钮。 它讨论了如何在代码和演示图板中使用按钮，并探讨了如何对按钮进行样式。
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/07/2017
ms.openlocfilehash: 07d48a38751680f5e7227682d0639b77b3a3627b
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436158"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>使用 Xamarin 中的 tvOS 按钮

使用类的实例 `UIButton` 在 tvOS 窗口中创建可获得焦点的可选择按钮。 当用户选择一个按钮时，它将向目标对象发送一个操作消息，以允许你的 tvOS 应用响应用户的输入。

[![示例按钮](buttons-images/buttons01.png)](buttons-images/buttons01.png#lightbox)

若要详细了解如何使用 Siri 遥控器并进行导航，请参阅使用 [导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md) 和 [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 文档。

<a name="About-Buttons"></a>

## <a name="about-buttons"></a>关于按钮

在 tvOS 中，按钮用于特定于应用程序的操作，并且可能包含标题、图标或两者。 当用户使用 [Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)浏览应用程序的用户界面时，焦点将移到给定按钮，使其更改文本和背景色。 阴影还适用于添加三维效果的按钮，使其看起来比用户界面的其余部分更高。

[![示例按钮](buttons-images/buttons01.png)](buttons-images/buttons01.png#lightbox)

Apple 对于使用按钮有以下建议：

- **使用 "标题" 或 "图标** "-当按钮中同时包含一个图标和标题时，空间会受到限制，因此请不要将两者组合在一起。
- **清晰标记破坏性按钮** -如果按钮执行破坏性操作 (如删除文件) ，请使用文本和/或图标清楚地将其标记为。 破坏性操作应始终向用户 [发出警报](~/ios/tvos/user-interface/alerts.md) ，要求用户限制该操作。
- **不要使用后退按钮** -Siri 遥控器上的菜单按钮用于返回到上一屏幕。 此规则的例外情况是用于应用内购买或应显示 " **取消** " 按钮的破坏性操作。

有关使用焦点和导航的详细信息，请参阅使用 [导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md) 文档。

<a name="Button-Icons"></a>

### <a name="button-icons"></a>按钮图标

Apple 建议你为按钮图标使用简单、高度可识别的图像。 很难在床上的房间内的电视屏幕上识别过于复杂的图标，因此，请尽可能使用最简单的表示形式。 尽可能使用图标的标准、清楚的图像 (如用于搜索) 的放大镜。

<a name="Button-Titles"></a>

### <a name="button-titles"></a>按钮标题

创建按钮标题时，Apple 提供以下建议：

- 在**图标按钮下方显示描述性文本**-如有可能，请将下面的描述性文本替换为仅图标按钮，以便进一步获取按钮的用途。
- 将**谓词或谓词短语用于标题**-清楚地陈述当用户单击按钮时将发生的操作。
- **使用 Title 样式大小写** -除了文章、连词或介词 (四个字母或更少的) 之外，按钮标题的每个单词都应大写。
- **使用简短的到点标题** -使用最短的措辞来描述按钮的操作。

<a name="Buttons-and-Storyboards"></a>

## <a name="buttons-and-storyboards"></a>按钮和情节提要

若要在 tvOS 应用中使用按钮，最简单的方法是使用 Xamarin Designer for iOS 将它们添加到应用的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **解决方案资源管理器**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 将一个 **按钮** 从 **库** 中拖放到视图上： 

    [![A 按钮](buttons-images/storyboard01.png)](buttons-images/storyboard01.png#lightbox)
1. 在 " **属性资源管理器**" 中，您可以调整按钮的几个属性，例如其 **标题** 和 **文本颜色**： 

    [![按钮属性](buttons-images/storyboard02.png)](buttons-images/storyboard02.png#lightbox)
1. 接下来，切换到 " **事件" 选项卡** ，然后将 **事件** 连接到 **按钮** ，并将其调用 `ButtonPressed` ： 

    [!["事件" 选项卡](buttons-images/storyboard03.png)](buttons-images/storyboard03.png#lightbox)
1. 你将自动切换到该 `ViewController.cs` 视图，你可以在其中使用 **向上** 和 **向下** 箭头键将新操作放置在代码中： 

    [![在代码中放置新操作](buttons-images/storyboard04.png)](buttons-images/storyboard04.png#lightbox)
1. 按 **enter** 选择位置： 

    [![代码编辑器](buttons-images/storyboard05.png)](buttons-images/storyboard05.png#lightbox)
1. 保存对所有文件所做的更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **解决方案资源管理器**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 将一个 **按钮** 从 **库** 中拖放到视图上： 

    [![A 按钮](buttons-images/storyboard01vs.png)](buttons-images/storyboard01vs.png#lightbox)
1. 在 " **属性资源管理器**" 中，您可以调整按钮的几个属性，例如其 **标题** 和 **文本颜色**： 

    [![属性资源管理器](buttons-images/storyboard02vs.png)](buttons-images/storyboard02vs.png#lightbox)
1. 接下来，切换到 " **事件" 选项卡** ，然后将 **事件** 连接到 **按钮** ，并将其调用 `ButtonPressed` ： 

    [!["事件" 选项卡](buttons-images/storyboard03vs.png)](buttons-images/storyboard03vs.png#lightbox)
1. 保存对所有文件所做的更改。

编辑视图控制器 (示例 `ViewController.cs`) 文件，并添加以下代码以处理所选的按钮：

```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

只要按钮的 `Enabled` 属性为 `true` ，并且它未被另一个控件或视图所覆盖，就可以使用 Siri Remote 使其成为焦点项。 如果用户选择该按钮并单击触摸面，则将 `ButtonPressed` 执行上面定义的操作。

> [!IMPORTANT]
> 尽管可以 `TouchUpInside` `UIButton` 在创建 **事件处理程序**时在 iOS 设计器中将操作分配给，但永远不会调用它，因为 Apple TV 没有触摸屏或支持触控事件。 为 tvOS 用户界面元素创建**操作**时，应始终使用默认**操作类型**。

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Buttons-and-Code"></a>

## <a name="buttons-and-code"></a>按钮和代码

（可选） `UIButton` 可以在 c # 代码中创建，并将其添加到 tvOS 应用的视图中。 例如：

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

当你在代码中创建新的时 `UIButton` ，请将其指定 `UIButtonType` 为以下内容之一：

- **系统** -这是 tvOS 提供的标准类型按钮，是最常使用的类型。
- **DetailDisclosure** -显示用于隐藏或显示详细信息的 "关闭" 按钮类型。
- **InfoDark** -"深色详细信息" 按钮在圆圈中显示为 "i"。
- **InfoLight** -在圆圈中显示一个 "i"。
- **AddContact** -将按钮显示为 "添加联系人" 按钮。
- **自定义** -允许你自定义按钮的多个特性。

接下来，定义按钮的屏幕大小和位置。 示例：

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

然后，设置按钮的标题。 `UIButtons` 与大多数控件不同 `UIKit` 的是，它们具有状态，因此您不能只是更改标题，而必须针对给定的更改标题 `UIControlState` 。 例如：

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

接下来，使用 `AllEvents` 事件查看用户单击了该按钮的时间。 示例：

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

最后，将该按钮添加到视图以显示该按钮：

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> 尽管可以将操作（如）分配给 `TouchUpInside` ，但 `UIButton` 永远不会调用它，因为 Apple TV 没有触摸屏或支持触控事件。 应始终使用 **AllEvents** 或 **PrimaryActionTriggered**等事件。

<a name="Styling-a-Button"></a>

## <a name="styling-a-button"></a>设置按钮样式

tvOS 提供了的多个属性 `UIButton` ，这些属性可用于提供其标题，并将其样式用于背景色和图像等。

<a name="Button-Titles"></a>

### <a name="button-titles"></a>按钮标题

如前文所述，与 `UIButtons` 大多数控件不同， `UIKit` 它们具有状态，因此您不能只是更改标题，而是必须针对给定的更改标题 `UIControlState` 。 例如：

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

您可以使用方法设置按钮的标题颜色 `SetTitleColor` 。 例如：

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

您可以使用调整标题的阴影 `SetTitleShadowColor` 。 例如：

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

使用以下代码突出显示按钮时，可以将标题阴影设置为从 *阴* 文更改为 *浮凸* ：

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

此外，您还可以使用特性化文本作为按钮的标题。 例如：

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>按钮图像

`UIButton`可以有附加的图像，并可以使用图像作为其背景。

若要为给定的设置按钮的背景图像 `UIControlState` ，请使用以下代码：

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

将 `AdjustsImageWhenHiglighted` 属性设置为，以在 `true` 突出显示按钮时将图像绘制为较浅 (这是默认) 。 将属性设置为以在 `AdjustsImageWhenDisabled` `true` 禁用按钮时将图像绘制为较暗 (，这是默认的) 。

若要设置按钮上显示的图像，请使用以下代码：

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

使用 `TintColor` 属性可以设置应用于标题和按钮图像的颜色色调。 对于类型的按钮 `Custom` ，此属性不起作用，你必须自行实现该 `TintColor` 行为。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何设计和使用 tvOS 应用程序中的按钮。 它演示了如何使用 iOS 设计器中的按钮，以及如何在 c # 代码中创建按钮。 最后，它演示了如何修改按钮的标题，并更改其样式和外观。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)