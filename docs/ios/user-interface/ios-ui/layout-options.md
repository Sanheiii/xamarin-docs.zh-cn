---
title: Xamarin 中的布局选项
description: 本文档介绍在 Xamarin 中布局用户界面的不同方式。 讨论自动调整和自动布局。
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 2514287fe06216d62b994cf19ba8f0901dd36c49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003329"
---
# <a name="layout-options-in-xamarinios"></a>Xamarin 中的布局选项

调整视图的大小或旋转时，有两种不同的机制来控制布局：

- **自动调整**–设计器中的自动调整检查器提供了一种设置 `AutoresizingMask` 属性的方法。 这会使控件锚定到其容器的边缘，并/或修复其大小。 自动调整适用于所有版本的 iOS。 下面更详细地介绍了这一点
- **自动布局**– iOS 6 中引入的一项功能，可用于对 UI 控件的关系进行精细的控制。 它将允许控制相对于设计图面上的其他元素的元素位置。 本主题将在[具有 Xamarin IOS 设计器指南的自动布局](~/ios/user-interface/designer/designer-auto-layout.md)中更详细地介绍。

## <a name="autosizing"></a>自动调整

当用户调整窗口大小时，如设备旋转和方向发生变化时，系统会根据自动调整规则自动调整该窗口内的视图的大小。 可以C#使用`UIView`的`AutoresizingMask`属性或 IOS 设计器的**Properties Pad**设置这些规则，如下所示：

 [![](layout-options-images/image41.png "Visual Studio for Mac Designer")](layout-options-images/image41.png#lightbox)

选择控件后，可以手动指定控件的位置和尺寸，还可以选择 "**自动调整**行为"。 如以下屏幕截图所示，我们可以使用自动调整控件中的弹簧和 struts 来定义所选视图与父视图的关系：

 [![](layout-options-images/image42.png "Visual Studio for Mac Designer")](layout-options-images/image42.png#lightbox)

调整*弹簧*会使视图根据其父视图的宽度或高度进行调整。 调整*strut*将使视图在该特定边缘上维持其自身与其父视图之间的恒定距离。

还可以在代码中设置这些设置：

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```

若要测试自动调整设置，请在项目的选项中启用不同的**受支持设备方向**：

 [![](layout-options-images/image43a.png "Autosizing Settings")](layout-options-images/image43a.png#lightbox)

在后面的代码中，我们可以使用以下代码，这会导致两个文本控件水平调整大小：

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```

我们还可以使用设计器调整控件。 选择如下所示的 "struts" 将使图像保持右对齐状态，而不会被剪裁到视图底部：

 [![](layout-options-images/autoresize.png "Autorotation")](layout-options-images/autoresize.png#lightbox)

这些屏幕截图显示了在屏幕旋转时控件如何调整大小或重新定位：

 [![](layout-options-images/image44a.png "Autorotation")](layout-options-images/image44a.png#lightbox)

请注意，由于 "`FlexibleWidth`" 设置的原因，文本视图和文本字段都延伸以保持相同的左右边距。 图像的上边缘和左边距是灵活的，这意味着它将保留下边距和右边距–旋转屏幕时，使图像处于视图中。 复杂布局通常需要在每个可见控件上组合这些设置，以使用户界面保持一致，并防止控件在视图的边界发生更改时（由于旋转或其他调整大小事件）。

## <a name="related-links"></a>相关链接

- [控件（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
