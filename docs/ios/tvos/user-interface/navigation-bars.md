---
title: 在 Xamarin 中使用 tvOS 导航栏
description: 本文档介绍如何在使用 Xamarin 生成的 tvOS 应用中使用导航栏。 它讨论了如何在情节提要中设置导航栏并响应这些按钮中的事件。
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 3ee159eaaf4a124f652b083e4dd7341909f52d82
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433128"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>在 Xamarin 中使用 tvOS 导航栏

可以将导航栏添加到视图顶部，以显示标题和可选导航栏按钮。 通常，当用户从主页（如表视图、集合或菜单）导航到显示所选项的详细信息的子视图时使用它们。

[![示例导航栏](navigation-bars-images/navbar01.png)](navigation-bars-images/navbar01.png#lightbox)

除了在中心) 中显示 (标题以外，导航栏还可以包含一个或多个导航栏按钮，)  (`UIBarButtonItem` 栏的左侧和右侧。

> [!IMPORTANT]
> 默认情况下，导航栏是完全透明的。 应注意确保导航栏的内容可在其下方的内容中保持可读。 例如，当表视图或集合中的内容在其下滚动时。

<a name="Navigation-Bars-and-Storyboards"></a>

## <a name="navigation-bars-and-storyboards"></a>导航栏和情节提要

若要在 tvOS 应用程序中使用导航栏，最简单的方法是使用 iOS 设计器将它们添加到应用的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，双击 " `Main.storyboard` 文件" 并将其打开以进行编辑。
1. 将 **导航栏** 从 **工具箱** 拖放到屏幕顶部的视图上：

    [![导航栏](navigation-bars-images/navbar02.png)](navigation-bars-images/navbar02.png#lightbox)
1. 双击 **导航栏** 以选择 **导航项**。 在**Properties Pad**的 "**小组件**" 选项卡中，可以设置**标题**：

    [![设置标题](navigation-bars-images/navbar03.png)](navigation-bars-images/navbar03.png#lightbox)
1. 接下来，可以将一个或多个 **条形按钮项** 添加到栏的任一端：

    [![条形按钮项](navigation-bars-images/navbar04.png)](navigation-bars-images/navbar04.png#lightbox)
1. 最后，在 "**属性资源管理器**" 的 "**事件**" 选项卡中将**条形按钮项**向上连接到操作：

    [![条形按钮项操作](navigation-bars-images/navbar05.png)](navigation-bars-images/navbar05.png#lightbox)
1. 保存所做更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **解决方案资源管理器**中，双击 " `Main.storyboard` 文件" 并将其打开以进行编辑。
1. 将 **导航栏** 从 **工具箱** 拖放到屏幕顶部的视图上：

    [![导航栏](navigation-bars-images/navbar02-vs.png)](navigation-bars-images/navbar02-vs.png#lightbox)
1. 双击 **导航栏** 以选择 **导航项**。 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，您可以设置**标题**：

    [![设置标题](navigation-bars-images/navbar03-vs.png)](navigation-bars-images/navbar03-vs.png#lightbox)
1. 接下来，可以将一个或多个 **条形按钮项** 添加到栏的任一端：

    [![条形按钮项](navigation-bars-images/navbar04-vs.png)](navigation-bars-images/navbar04-vs.png#lightbox)
1. 最后，在 "**属性资源管理器**" 的 "**事件**" 选项卡中将**条形按钮项**向上连接到操作：

    [![条形按钮项操作](navigation-bars-images/navbar05-vs.png)](navigation-bars-images/navbar05-vs.png#lightbox)
1. 保存所做更改。

-----

> [!IMPORTANT]
> 尽管可以 `TouchUpInside` 在 IOS 设计器中将事件（例如 UIButton) ）分配给 UI (元素，但绝不会调用此类事件，因为 APPLE TV 没有触摸屏或支持触控事件。 `Primary Action`创建 tvOS 用户界面元素的事件处理程序时，应始终使用事件。

下面的代码提供了三个不同 BarButtonItems 上的事件处理程序的示例： `ShowFirstHotel` 、 `ShowSecondHotel` 和 `ShowThirdHotel` 。 单击每个项目时，背景图像 `HotelImage` 将更改。 此操作在视图控制器 (示例) 文件中进行编辑 `ViewController.cs` ：

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

只要按钮的 `Enabled` 属性为 `true` ，并且它未被另一个控件或视图所覆盖，就可以使用 Siri Remote 使其成为焦点项。

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用程序中设计和使用导航栏。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)