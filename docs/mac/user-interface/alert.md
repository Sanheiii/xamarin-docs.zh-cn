---
title: Xamarin 中的警报
description: '本文介绍如何在 Xamarin. Mac 应用程序中使用警报。 它介绍了如何创建和显示 c # 代码中的警报以及如何响应用户交互。'
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 55c4d4e6154ef75f15b2e75edc020b7cb4c96e38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435305"
---
# <a name="alerts-in-xamarinmac"></a>Xamarin 中的警报

_本文介绍如何在 Xamarin. Mac 应用程序中使用警报。它介绍了如何创建和显示 c # 代码中的警报以及如何响应用户交互。_

在 Xamarin 应用 *程序中使用* c # 和 .net 时，可以访问 *Xcode 和* 中的开发人员所使用的相同警报。 

警报是一种特殊类型的对话框，在出现严重问题时出现 (例如错误) 或警告 (如准备删除文件) 。 由于警报是一个对话框，因此它还需要用户响应才能关闭。

[![示例警报](alert-images/alert06.png)](alert-images/alert06.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用警报的基本知识。 

<a name="Introduction_to_Alerts"></a>

## <a name="introduction-to-alerts"></a>警报简介

警报是一种特殊类型的对话框，在出现严重问题时出现 (例如错误) 或警告 (如准备删除文件) 。 由于警报会干扰用户，因此在用户可以继续使用其任务之前，请避免显示警报，除非绝对必要。

Apple 建议遵循以下准则：

- 请勿只使用警报来向用户显示信息。
- 不要为常见的可撤消操作显示警报。 即使这种情况可能会导致数据丢失。
- 如果需要警报，请避免使用任何其他 UI 元素或方法来显示警报。
- 应慎用警告图标。
- 清楚地说明警报的情况，并在警报消息中简洁地说明。
- 默认按钮名称应对应于在警报消息中描述的操作。

有关详细信息，请参阅 Apple 的[操作系统 X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的 "[警报](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1)" 部分

<a name="Anatomy_of_an_Alert"></a>

## <a name="anatomy-of-an-alert"></a>警报分析

如上所述，当出现严重问题时，应向应用程序的用户显示警报，或在出现数据丢失 (例如关闭未保存的文件) 时显示警报。 在 Xamarin 中，将在 c # 代码中创建警报，例如：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Critical,
  InformativeText = "We need to save the document here...",
  MessageText = "Save Document",
};
alert.RunModal ();
```

上面的代码将显示一个警报，其中的应用程序图标叠加在警告图标、标题、警告消息和单个 **"确定"** 按钮上：

[![带有 "确定" 按钮的警报](alert-images/alert01.png)](alert-images/alert01.png#lightbox)

Apple 提供了几个可用于自定义警报的属性：

- **AlertStyle** 定义警报的类型，如下所示：
  - **警告** -用于警告用户当前或即将发生的事件，该事件不是关键事件。 这是默认样式。
  - **信息** -用于警告用户当前或即将发生的事件。 当前，**警告**与**信息性**之间没有明显的差异
  - **关键** -用于警告用户即将发生的事件的严重后果 (如删除文件) 。 应慎用此类警报。
- **MessageText** -这是警报的主要消息或标题，应该快速定义用户的情况。
- **InformativeText** -这是警报的正文，你应清楚地定义情况并向用户显示可行的选项。
- **图标** -允许向用户显示自定义图标。
- **HelpAnchor**  & **ShowsHelp** -允许将警报绑定到应用程序 HelpBook，并显示警报的帮助。
- **按钮** -默认情况下，警报只包含 **"确定"** 按钮，但 " **按钮** " 集合允许您根据需要添加更多选项。
- **ShowsSuppressionButton** -如果 `true` 显示复选框，用户可以使用此复选框来取消警报，以便在触发警报时的后续事件发生。
- **AccessoryView** -允许将另一个子视图附加到警报以提供额外的信息，例如添加用于数据输入的 **文本字段** 。 如果设置了新的 **AccessoryView** 或修改现有的，则需要调用 `Layout()` 方法以调整警报的可见布局。

<a name="Displaying_an_Alert"></a>

## <a name="displaying-an-alert"></a>显示警报

可以通过两种不同的方式显示警报：自由浮动或作为工作表。 以下代码将以自由浮动方式显示警报：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.RunModal ();
```

如果运行此代码，将显示以下内容：

[![简单警报](alert-images/alert02.png)](alert-images/alert02.png#lightbox)

下面的代码显示与工作表相同的警报：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

如果运行此代码，将显示以下内容：

[![显示为工作表的警报](alert-images/alert03.png)](alert-images/alert03.png#lightbox)

<a name="Working_with_Alert_Buttons"></a>

## <a name="working-with-alert-buttons"></a>使用警报按钮

默认情况下，警报只显示 **"确定"** 按钮。 但是，您并不局限于这一点，您可以通过将它们追加到 " **按钮** " 集合来创建额外的按钮。 下面的代码使用 **"确定"、"取消"** 和 " **Cancel** **可能**" 按钮创建一个自由浮动的警报：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

添加的第一个按钮是 _默认按钮_ ，当用户按 Enter 键时，该按钮将被激活。 返回的值将是一个整数，表示用户按下的按钮。 在本例中，将返回以下值：

- **正常** -1000。
- **取消** -1001。
- **可能** 为-1002。

如果运行此代码，将显示以下内容：

[![带有三个按钮选项的警报](alert-images/alert04.png)](alert-images/alert04.png#lightbox)

下面是与工作表相同的警报的代码：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}", result);
});
```

如果运行此代码，将显示以下内容：

[![显示为工作表的三个按钮警报](alert-images/alert05.png)](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> 永远不要将三个以上的按钮添加到警报。

<a name="Showing_the_Suppress_Button"></a>

## <a name="showing-the-suppress-button"></a>显示 "取消" 按钮

如果警报的 `ShowSuppressButton` 属性为 `true` ，则该警报会显示一个复选框，用户可以使用该复选框来取消警报，以便在触发警报时的后续事件中出现。 下面的代码使用 "取消" 按钮显示一个自由浮动的警报：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

如果的值 `alert.SuppressionButton.State` 为，则 `NSCellStateValue.On` 用户已选中 "取消隐藏" 复选框，否则它们不存在。

如果代码正在运行，将显示以下内容：

[![带有 "取消" 按钮的警报](alert-images/alert06.png)](alert-images/alert06.png#lightbox)

下面是与工作表相同的警报的代码：

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

如果运行此代码，将显示以下内容：

[![带有 "取消" 按钮显示为工作表的警报](alert-images/alert07.png)](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView"></a>

## <a name="adding-a-custom-subview"></a>添加自定义子视图

警报有一个 `AccessoryView` 属性，该属性可用于进一步自定义警报，并为用户输入添加类似于 **文本字段** 的内容。 下面的代码使用已添加的文本输入字段创建一个自由浮动的警报：

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

此处的关键行 `var input = new NSTextField (new CGRect (0, 0, 300, 20));` 用于创建将添加警报的新 **文本字段** 。 `alert.AccessoryView = input;` 这会将 **文本字段** 附加到警报，并调用 `Layout()` 方法，这是调整警报大小以适应新的子视图所必需的。

如果运行此代码，将显示以下内容：

[![如果运行此代码，将显示以下](alert-images/alert08.png)](alert-images/alert08.png#lightbox)

下面是与工作表相同的警报：

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
  Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

如果运行此代码，将显示以下内容：

[![带有自定义视图的警报](alert-images/alert09.png)](alert-images/alert09.png#lightbox)

<a name="Summary"></a>

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用警报。 我们看到了不同的警报类型和用法，如何创建和自定义警报，以及如何在 c # 代码中使用警报。

## <a name="related-links"></a>相关链接

- [MacWindows (示例) ](/samples/xamarin/mac-samples/macwindows)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)