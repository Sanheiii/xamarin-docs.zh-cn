---
title: iOS 6 简介
description: 本文档链接到介绍 iOS 6 中引入的功能的指南。 集合视图、PassKit、社交框架，以及对 StoreKit 的更改都进行了讨论。
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: be78e76e2a52fb6e924fd67e3f0de9e0890ee25b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933427"
---
# <a name="introduction-to-ios-6"></a>iOS 6 简介

_iOS 6 包括多种用于开发应用的新技术，即 iOS 6 向 c # 开发人员提供。_

[![IOS 6 徽标](images/ios6-large.jpg)](images/ios6-large.jpg#lightbox)

使用 iOS 6 和 Xamarin iOS 6，开发人员现在可以使用丰富的功能来创建 iOS 应用程序，其中包括面向 iPhone 5 的应用程序。
本文档列出了一些更激动人心的新功能，以及每个主题的文章的链接。 此外，它还涉及到几项更改，这些更改在开发人员转向 iOS 6 和新的 iPhone 5 解决方案时很重要。

## <a name="introduction-to-collection-views"></a>[集合视图简介](~/ios/user-interface/controls/uicollectionview.md)

集合视图允许使用任意布局显示内容。 它们允许轻松地在框中创建类似于网格的布局，同时还支持自定义布局。 有关详细信息，请参阅[集合视图简介](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md) 指南。

## <a name="introduction-to-passkit"></a>[PassKit 简介](~/ios/platform/passkit.md)

PassKit 框架允许应用程序与 Passbook 应用程序中管理的数字传递交互。 有关详细信息，请参阅[Pass 工具包简介指南](~/ios/platform/passkit.md)。

## <a name="introduction-to-eventkit"></a>[EventKit 简介](~/ios/platform/eventkit.md)

EventKit 框架提供了一种方法，用于访问日历数据库存储的日历、日历事件和提醒数据。 自 iOS 4 起，对日历和日历事件的访问已可用，但 iOS 6 现在公开对提醒数据的访问。 有关详细信息，请参阅[I](~/ios/platform/eventkit.md) [ntroduction to EventKit](~/ios/platform/eventkit.md) guide。

## <a name="introduction-to-the-social-framework"></a>[社交框架简介](~/ios/platform/social-framework.md)

社交框架提供了一个统一的 API，用于与中国的用户（包括 Twitter 和 Facebook）以及 SinaWeibo 的社交网络交互。 有关详细信息，请参阅[社交框架简介](~/ios/platform/social-framework.md)指南。

## <a name="changes-to-storekit"></a>[对 StoreKit 的更改](changes-to-storekit.md)

Apple 在应用商店工具包中引入了两项新功能：从应用中购买和下载 iTunes 或应用商店内容，并托管应用内购买内容文件！。 有关详细信息，请参阅[应用商店工具包](changes-to-storekit.md)指南。

## <a name="other-changes"></a>其他更改

### <a name="viewwillunload-and-viewdidunload-deprecated"></a>不推荐使用 ViewWillUnload 和 ViewDidUnload

在 `ViewWillUnload` `ViewDidUnload` `UIViewController` iOS 6 中不再调用的和方法。 在以前版本的 iOS 中，应用程序可能已使用这些方法在卸载视图之前保存状态，并分别清理代码。

例如，Visual Studio for Mac 会创建一个名为的方法 `ReleaseDesignerOutlets` （如下所示），然后从该方法调用 `ViewDidUnload` ：

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

但是，在 iOS 6 中，不再需要调用 `ReleaseDesignerOutlets` 。   

对于清理代码，iOS 6 应用程序应使用 `DidReceiveMemoryWarning` 。 但是，调用的代码 `Dispose` 应慎用，只应用于内存密集型对象，如下所示：

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

同样， `Dispose` 几乎不需要按上述方式调用。 通常，大多数应用程序都应该删除事件处理程序。

对于保存状态的情况，应用程序可以在和中执行此， `ViewWillDisappear` `ViewDidDisappear` 而不是 `ViewWillUnload` 。

### <a name="iphone-5-resolution"></a>iPhone 5 解决方案

iPhone 5 设备有640x1136 的分辨率。 当在 iPhone 5 上运行时，面向以前版本的 iOS 的应用程序将显示为 letterboxed，如下所示：

 [![当在 iPhone 5 上运行时，面向以前版本的 iOS 的应用程序将显示为 letterboxed](images/01-letterboxed.png)](images/01-letterboxed.png#lightbox)

为了使应用程序在 iPhone 5 上全屏显示，只需添加一个名为 " `Default-568h@2x.png` 640x1136" 的映像。 下面的屏幕截图显示在包含此映像之后运行的应用程序：

 [![此屏幕截图显示在包含此映像之后运行的应用程序](images/02-fullscreen.png)](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>子类 UINavigationBar

在 iOS 6 中 `UINavigationBar` 可以是子类。 这允许对的外观进行更多的控制 `UINavigationBar` 。 例如，应用程序可以为添加子视图提供子类，为这些视图添加动画并修改的边界 `UINavigationBar` 。

下面的代码显示了一个 `UINavigationBar` 添加了的子类的示例 `UIImageView` ：

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

若要将子类添加 `UINavigationBar` 到 `UINavigationController` ，请使用 `UINavigationController` 采用和类型的构造函数 `UINavigationBar` `UIToolbar` ，如下所示：

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

使用此 `UINavigationBar` 子类将导致显示图像视图，如以下屏幕截图所示：

 [![使用此 UINavigationBar 子类会导致显示图像视图，如此屏幕截图所示](images/03-navbar.png)](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>界面方向

在 iOS 6 应用程序可以重写之前 `ShouldAutorotateToInterfaceOrientation` ，对于支持的特定控制器，返回 true。 例如，以下代码将仅用于支持纵向：

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

在 iOS 6 中 `ShouldAutorotateToInterfaceOrientation` 已弃用。
相反，应用程序可以 `GetSupportedInterfaceOrientations` 在根视图控制器上重写，如下所示：

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

在 iPad 上，如果未实现，则默认为全部四个方向 `GetSupportedInterfaceOrientation` 。 在 iPhone 和 iPod touch 上，默认值为除之外的所有方向 `PortraitUpsideDown` 。
