---
title: 在 Xamarin 中显示警报
description: 本文档介绍如何使用 iOS 8 中引入的 UIAlertController Api 在 Xamarin 中显示警报。
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: b7dae8afbb5db378687f9ecb9469236dc68831ae
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435485"
---
# <a name="displaying-alerts-in-xamarinios"></a>在 Xamarin 中显示警报

从 iOS 8 开始，UIAlertController 已完成替换为 UIActionSheet 和 UIAlertView，这两者现已弃用。

不同于它所替换的类（UIView 的子类），UIAlertController 是 UIViewController 的子类。

用于 `UIAlertControllerStyle` 指示要显示的警报的类型。 这些警报类型为：

- **UIAlertControllerStyleActionSheet**
  - IOS 8 之前，这会是一个 UIActionSheet
- **UIAlertControllerStyleAlert**
  - 预 iOS 8 此项会被 UIAlertView 

创建警报控制器时需要执行三个必要的步骤：

- 使用以下内容创建并配置警报：
  - title
  - message
  - preferredStyle

-  (可选) 添加文本字段
- 添加所需的操作
- 显示视图控制器

最简单的警报包含一个按钮，如以下屏幕截图所示：

 ![带有一个按钮的警报](alerts-images/alert1.png)

显示简单警报的代码如下所示：

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

显示包含多个选项的警报的方式类似，但添加了两个操作。 例如，以下屏幕截图显示了包含两个按钮的警报：

 ![具有两个按钮的警报](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

警报还可以显示操作表，类似于下面的屏幕截图：

 ![操作表警报](alerts-images/alert3.png)

将按钮添加到警报， `AddAction` 方法如下：

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>相关链接

- [控制 (示例) ](/samples/xamarin/ios-samples/controls)
- [警报控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)