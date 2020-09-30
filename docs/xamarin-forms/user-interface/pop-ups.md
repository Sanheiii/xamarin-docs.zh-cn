---
title: 显示弹出窗口
description: Xamarin.Forms 提供三个类似于弹出式的用户界面元素-警报、操作表和提示。 本文演示如何使用警报、操作表和提示 Api 来显示询问用户简单问题的对话框、指导用户完成任务以及显示提示。
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 58ab2f785c26019c90b91c928daed864c3c6098b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559566"
---
# <a name="display-pop-ups"></a>显示弹出窗口

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

显示警报，要求用户进行选择或显示提示是常见的 UI 任务。 Xamarin.Forms 在类上有三种方法 [`Page`](xref:Xamarin.Forms.Page) ，可通过弹出窗口与用户进行交互： [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 、 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 和 `DisplayPromptAsync` 。 在每个平台上使用相应的本机控件呈现。

## <a name="display-an-alert"></a>显示警报

所有 Xamarin.Forms 受支持的平台都有一个模式弹出窗口，用于向用户发出警报，或向他们询问简单的问题。 若要在中显示这些警报 Xamarin.Forms ，请 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 在任何上使用方法 [`Page`](xref:Xamarin.Forms.Page) 。 以下代码行向用户显示一个简单的消息：

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![带有按钮的警告对话框](pop-ups-images/alert.png)

本例不收集用户的信息。 警报以模式显示，一旦用户被解雇，就会继续与应用程序进行交互。

[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*)方法还可用于通过呈现两个按钮并返回来捕获用户的响应 `boolean` 。 要从警报中获得响应，请为两个按钮和 `await` 方法提供文本。 在用户选择其中一个选项后，答案将返回到你的代码。 注意下面示例代码中的 `async` 和 `await` 关键字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "包含两个按钮的警报对话框")](pop-ups-images/alert2.png#lightbox "包含两个按钮的警报对话框")

## <a name="guide-users-through-tasks"></a>指导用户完成任务

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) 是 iOS 中的常见 UI 元素。 Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法允许将此控件包含在跨平台应用程序中，从而在 ANDROID 和 UWP 中呈现本机替代项。

要显示操作表（任何 [`Page`](xref:Xamarin.Forms.Page) 中的 `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)），请将消息和按钮标签作为字符串传递。 该方法返回供用户单击的按钮的字符串标签。 下面是简单示例：

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![ActionSheet 对话框](pop-ups-images/action.png)

`destroy` 按钮的呈现方式与其他按钮不同，可以保留 `null` 或指定为第三个字符串参数。 下面的示例使用 `destroy` 按钮：

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "带有销毁按钮的操作表对话框")](pop-ups-images/action2.png#lightbox "带有销毁按钮的操作表对话框")

## <a name="display-a-prompt"></a>显示提示

若要显示提示，请调用 `DisplayPromptAsync` in any [`Page`](xref:Xamarin.Forms.Page) ，同时传递标题和消息作为 `string` 参数：

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

提示符以模式方式显示：

[![IOS 和 Android 上的模式提示屏幕截图](pop-ups-images/simple-prompt.png "模式提示")](pop-ups-images/simple-prompt-large.png#lightbox "模式提示")

如果点击 "确定" 按钮，则输入的响应将返回为 `string` 。 如果点击 "取消" 按钮， `null` 则返回。

该方法的完整参数列表 `DisplayPromptAsync` 为：

- `title`类型为的， `string` 它是要在提示中显示的标题。
- `message`类型为的， `string` 是要在提示中显示的消息。
- `accept`类型为的， `string` 它是 "接受" 按钮的文本。 这是一个可选参数，其默认值为 "正常"。
- `cancel`类型为的， `string` 它是 "取消" 按钮的文本。 这是一个可选参数，其默认值为 "取消"。
- `placeholder`类型为的， `string` 它是要在提示中显示的占位符文本。 这是一个可选参数，其默认值为 `null` 。
- `maxLength`类型为的 `int` 最大长度为用户响应的最大长度。 这是一个可选参数，其默认值为-1。
- `keyboard`类型为的，用于 `Keyboard` 用户响应的键盘类型。 这是一个可选参数，其默认值为 `Keyboard.Default` 。
- `initialValue`类型为的 `string` 预定义响应将显示，并且可以编辑。 这是一个可选参数，其默认值为空 `string` 。

下面的示例演示如何设置一些可选参数：

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

此代码显示预定义的10个响应，限制可输入的字符数2，并显示用户输入的数字键盘：

[![IOS 和 Android 上的模式提示屏幕截图](pop-ups-images/keyboard-prompt.png "模式提示")](pop-ups-images/keyboard-prompt-large.png#lightbox "模式提示")

## <a name="related-links"></a>相关链接

- [PopupsSample](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)