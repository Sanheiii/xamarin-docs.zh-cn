---
title: Xamarin 中的3D 触控简介
description: 本文介绍如何使用 iPhone 6s 和 iPhone 6s Plus 引入的3D 触摸手势。 这些手势可实现压力敏感度、速览和 pop 以及快速操作。
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 6e76821033276ed3bb756cecd10e801fa615fb1e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431834"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Xamarin 中的3D 触控简介

_本文介绍如何在应用中使用新的 iPhone 6s 和 iPhone 6s 以及3D 触摸手势。_

[![启用 3D Touch 的应用的示例](3d-touch-images/info01.png)](3d-touch-images/info01.png#lightbox)

本文将介绍如何使用新的 3D Touch Api 向在新的 iPhone 6s 和 iPhone 6s Plus 设备上运行的 Xamarin 应用程序添加压力敏感度。

使用3D 触控，iPhone 应用现在不仅可以知道用户正在触摸设备的屏幕，还可以判断用户施加的压力，并响应不同的压力级别。

3D Touch 为你的应用提供以下功能：

- [压力敏感度](#Pressure-Sensitivity) -应用现在可以测量用户在屏幕上的接触速度，并利用这些信息。
  例如，绘制应用可以根据用户对屏幕的接触情况，使线条更粗或更细。
- "[速览" 和 "弹出](#Peek-and-Pop)"-应用现在可以让用户与其数据交互，而无需导航到当前上下文。 通过在屏幕上按 "硬"，可以查看他们感兴趣的项目 (如) 预览消息。 通过按更难，他们可以弹出项目。
- "[快速操作](#Quick-Actions)"-当用户在桌面应用中右键单击某一项时，可以使用快捷菜单，如可以弹出的上下文菜单。
  使用 "快速操作"，可以直接从主屏幕上的应用程序图标添加应用程序中函数的快捷方式。
- [在模拟器中测试3D 触摸](#Testing-3D-Touch-in-the-Simulator) -通过正确的 Mac 硬件，你可以在 iOS 模拟器中测试已启用 3d touch 的应用。

<a name="Pressure-Sensitivity"></a>

## <a name="pressure-sensitivity"></a>压力敏感度

如上所述，通过使用 [UITouch](xref:UIKit.UITouch) 类的新属性，可以测量用户应用到 iOS 设备屏幕的压力量，并在用户界面中使用此信息。 例如，根据压力量使画笔笔划更半透明或不透明。

[![根据压力量呈现为更半透明或不透明的画笔笔划](3d-touch-images/pressure01.png)](3d-touch-images/pressure01.png#lightbox)

3D 触摸的结果是，如果你的应用在 iOS 9 上运行 (或更高) 并且 iOS 设备能够支持3D 触摸，则压力更改将导致 `TouchesMoved` 引发事件。

例如，在监视 UIView 的 `TouchesMoved` 事件时， [UIView](xref:UIKit.UIView)可以使用以下代码来获取用户应用到屏幕的当前压力：

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

`MaximumPossibleForce`属性 `Force` 根据运行应用的 iOS 设备返回[UITouch](xref:UIKit.UITouch)的属性的最大可能值。

> [!IMPORTANT]
> 压力更改将导致 `TouchesMoved` 引发事件，即使 X/Y 坐标未更改也是如此。 由于这种行为发生了这种更改，因此应为 iOS 应用做好准备，以便 `TouchesMoved` 更频繁地调用事件，并使 X/Y 坐标与上次调用相同 `TouchesMoved` 。

有关详细信息，请参阅 Apple 的 [TouchCanvas：使用 UITouch 高效有效地使用](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) 示例应用和 [UITouch 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop"></a>

## <a name="peek-and-pop"></a>速览和 Pop

3D Touch 提供了新的方法，使用户能够比以往更快地与应用程序中的信息交互，而不必从当前位置导航。

例如，如果您的应用程序显示一张消息，则用户可以按下某个项目的 "hard" 来预览覆盖 *视图)  (* 其内容。

[![查看内容的示例](3d-touch-images/peekandpop01.png)](3d-touch-images/peekandpop01.png#lightbox)

如果用户按 "困难"，则会在 ") " 视图中输入常规消息视图 (称为 " *弹出*ping"。

### <a name="checking-for-3d-touch-availability"></a>检查3D 触摸可用性

使用时， `UIViewController` 可以使用以下代码来查看运行应用的 iOS 设备是否支持3D 触摸：

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

此方法可以在之前 *或之后*调用 `ViewDidLoad()` 。

### <a name="handling-peek-and-pop"></a>处理速览和 Pop

在可以处理三维触控的 iOS 设备上，我们可以使用类的实例  `UIViewControllerPreviewingDelegate` 来处理 **速览** 和 **Pop** 项详细信息的显示。 例如，如果有一个名为的表视图控制器，则 `MasterViewController` 可以使用以下代码来支持 **速览** 和 **Pop**：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

`GetViewControllerForPreview`方法用于执行**查看**操作。 它可以访问表单元和备份数据，然后 `DetailViewController` 从当前情节提要加载。 通过将设置 `PreferredContentSize` 为 (0，0) 正在请求默认 **速览** 视图大小。 最后，我们将显示所有内容（但不显示的单元格）， `previewingContext.SourceRect = cell.Frame` 并返回显示的新视图。

重新显示 `CommitViewController` 在用户按下 "更难" 时**Pop**在 "**查看**" 视图中创建的视图。

### <a name="registering-for-peek-and-pop"></a>注册速览和 Pop

从我们希望允许用户 **查看** 和 **弹出** 项目的视图控制器，我们需要注册此服务。 在上述示例中， () 的表视图控制器的示例中 `MasterViewController` ，我们将使用以下代码：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

在这里，我们将 `RegisterForPreviewingWithDelegate` 使用我们在上面创建的实例调用方法 `PreviewingDelegate` 。 在支持3D 触摸的 iOS 设备上，用户可以按下某个项目的 hard 来查看它。 如果他们按更难，此项将弹出到它的 "标准显示" 视图中。

有关详细信息，请参阅 [iOS 9 ApplicationShortcuts 示例](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) 和 Apple 的 [ViewControllerPreviews：使用 UIViewController 预览 api](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) 示例应用、 [UIPreviewAction 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)、 [UIPreviewActionGroup 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) 和 [UIPreviewActionItem 协议参考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions"></a>

## <a name="quick-actions"></a>快速操作

使用3D 触摸和快速操作，你可以通过 iOS 设备上的主屏幕图标从你的应用程序中添加常用、快速且易于访问的功能的快捷方式。

如上所述，可以考虑在用户右键单击桌面应用程序中的某一项时，可以弹出的上下文菜单等快速操作。 您应该使用 "快速操作" 提供应用程序的最常见函数或功能的快捷方式。

[!["快速操作" 菜单示例](3d-touch-images/quickactions01.png)](3d-touch-images/quickactions01.png#lightbox)

### <a name="defining-static-quick-actions"></a>定义静态快速操作

如果你的应用程序所需的一个或多个快速操作是静态的，并且无需更改，你可以在应用的文件中定义它们 `Info.plist` 。 在外部编辑器中编辑此文件并添加以下项：

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

此处，我们将定义两个静态快速操作项，其中包含以下项：

- `UIApplicationShortcutItemIconType` -将 "快速操作" 项显示的图标定义为下列值之一：
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

  ![UIApplicationShortcutIconType 图像](3d-touch-images/uiapplicationshortcuticontype.png)

- `UIApplicationShortcutItemSubtitle` -定义项的副标题。
- `UIApplicationShortcutItemTitle` -定义项的标题。
- `UIApplicationShortcutItemType` -一个字符串值，我们将使用它来确定应用程序中的项。 有关详细信息，请参阅下一节。

> [!IMPORTANT]
> `Info.plist`无法通过属性访问文件中设置的快速操作快捷方式项 `Application.ShortcutItems` 。 它们只传递到 `HandleShortcutItem` 事件处理程序。

### <a name="identifying-quick-action-items"></a>正在识别快速操作项

如上所示，当你在应用程序中定义了快速操作项时 `Info.plist` ，将为该密钥分配一个字符串值 `UIApplicationShortcutItemType` 以标识它们。

若要使这些标识符更易于在代码中使用，请将名为的类添加 `ShortcutIdentifier` 到应用的项目中，使其类似于以下内容：

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action"></a>

### <a name="handling-a-quick-action"></a>处理快速操作

接下来，需要修改应用的文件， `AppDelegate.cs` 以处理用户从主屏幕上应用的图标中选择 "快速操作" 项的用户。

进行以下编辑：

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

首先，我们定义一个公共 `LaunchedShortcutItem` 属性以跟踪用户最后选定的 "快速操作" 项。 然后，重写 `FinishedLaunching` 方法，并查看是否 `launchOptions` 已传递以及是否包含 "快速操作" 项。 如果是这样，请在属性中存储 "快速操作" `LaunchedShortcutItem` 。

接下来，我们将重写 `OnActivated` 方法，并将所选的任何快速启动项传递到要操作的 `HandleShortcutItem` 方法。 目前，我们仅向 **控制台**写入一条消息。 在实际应用中，您将处理所需的操作。 执行该操作后，将清除该 `LaunchedShortcutItem` 属性。

最后，如果你的应用程序已在运行，则会 `PerformActionForShortcutItem` 调用方法来处理快速操作项，因此我们需要替代它并在 `HandleShortcutItem` 此处调用方法。

### <a name="creating-dynamic-quick-action-items"></a>正在创建动态快速操作项

除了在应用文件中定义静态快速操作项外 `Info.plist` ，还可以快速创建动态动态操作。 若要定义两个新的动态快速操作，请 `AppDelegate.cs` 再次编辑文件，然后修改方法，使其类似于 `FinishedLaunching` 以下内容：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

现在，我们要检查是否 `application` 已包含一组动态创建的 `ShortcutItems` ，如果不是，我们将创建两个新 `UIMutableApplicationShortcutItem` 的对象来定义新项目并将其添加到 `ShortcutItems` 数组中。

我们在 [处理 "快速操作](#Handling-a-Quick-Action) " 部分中添加的代码将处理这些动态快速操作，就像静态的操作一样。

应注意的是，可以创建静态和动态快速操作项的组合 (在此处) ，你并不局限于其中一项。

有关详细信息，请参阅 [iOS 9 ViewControllerPreview 示例](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) 并参阅 Apple 的 [ApplicationShortcuts：使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) 示例应用、 [UIApplicationShortcutItem 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)、 [UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) 类引用和 [UIApplicationShortcutIcon 类引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)。

<a name="Testing-3D-Touch-in-the-Simulator"></a>

## <a name="testing-3d-touch-in-the-simulator"></a>在模拟器中测试 3D Touch

如果在兼容的 Mac 上使用最新版本的 Xcode 和 iOS 模拟器，并 Force Touch 启用触控板，则可以在模拟器中测试3D 触摸功能。

若要启用此功能，请在模拟的 iPhone 硬件中运行支持 3D Touch (iPhone 6s 和更高) 的任何应用。 接下来，选择 iOS 模拟器中的 " **硬件** " 菜单，并启用 " **对3d 触摸菜单项使用触控板强制** " 菜单项：

[![选择 iOS 模拟器中的 "硬件" 菜单，并启用 "对3D 触摸菜单项使用触控板强制" 菜单项](3d-touch-images/simulator01.png)](3d-touch-images/simulator01.png#lightbox)

此功能处于活动状态时，你可以在 Mac 的触控板上按更难的方式启用3D 触摸，就像在实际的 iPhone 硬件上一样。

## <a name="summary"></a>总结

本文介绍了在 iOS 9 中提供的新 3D Touch Api，适用于 iPhone 6s 和 iPhone 6s Plus。 它涵盖了向应用程序添加压力敏感度。使用速览和 Pop 快速显示当前上下文中的应用内信息，而无需导航;并使用 "快速操作" 提供应用最常用功能的快捷方式。

## <a name="related-links"></a>相关链接

- [iOS 9 ViewControllerPreview 示例](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [iOS 9 ApplicationShortcuts 示例](/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [准备好 iPhone 应用以便进行3D 触控](https://developer.apple.com/ios/3d-touch/)