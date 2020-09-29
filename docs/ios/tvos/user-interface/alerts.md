---
title: 在 Xamarin 中使用 tvOS 警报
description: 本文档介绍如何在 Xamarin 中使用 tvOS 警报。 它讨论了如何显示警报、添加文本字段和帮助器类。
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 83831b71ebb349873b2a77bbd77ba08e08002d4a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436606"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>在 Xamarin 中使用 tvOS 警报

_本文介绍如何使用 UIAlertController 在 tvOS 中向用户显示警报消息。_

如果需要 tvOS 用户或请求权限来执行破坏性操作 (如删除文件) ，可以使用以下内容显示警报消息 `UIAlertViewController` ：

[![示例 UIAlertViewController](alerts-images/alert01.png)](alerts-images/alert01.png#lightbox)

如果除了显示消息以外，还可以向警报添加按钮和文本字段，以允许用户响应操作并提供反馈。

<a name="About-Alerts"></a>

## <a name="about-alerts"></a>关于警报

如上所述，警报用于使用户注意，并通知用户应用状态或请求反馈。 警报必须包含一个标题，可以有选择地包含一个消息和一个或多个按钮或文本字段。

[![示例警报](alerts-images/alert04.png)](alerts-images/alert04.png#lightbox)

Apple 提供了以下有关使用警报的建议：

- **使用警报** 时，警报会中断用户与应用的流动，并使用户体验中断，因此，只应用于错误通知、应用内购买和破坏性操作等重要情况。
- **提供了有用的选择** -如果警报向用户提供选项，您应确保每个选项都提供关键信息并为用户提供有用的操作。

<a name="Alert-Titles-and-Messages"></a>

### <a name="alert-titles-and-messages"></a>提醒标题和消息

Apple 提供以下建议来演示警报的标题和可选消息：

- **使用 Multiword 标题** -警报的标题应当清楚地了解状况，同时保持简单。 单个单词标题很少提供足够的信息。
- 请尽可能**使用不需要消息的描述性标题**，请考虑使警报的标题具有足够的描述性，指出不需要可选消息文本。
- 将**消息设置为简短、完整的句子**-如果需要使用可选的消息来使警报点处于最佳位置，请尽可能简化，并使其成为具有正确大小写和标点的完整句子。

<a name="Alert-Buttons"></a>

### <a name="alert-buttons"></a>警报按钮

Apple 对于向警报添加按钮有以下建议：

- **限制为两个按钮** -尽可能将警报限制为最多两个按钮。 单按钮警报提供信息，而不提供操作。 两个按钮警报提供简单的 "是/否" 操作选择。
- **使用简洁的逻辑按钮标题** -简单一到两个单词按钮标题，可清楚地说明按钮的操作效果。 有关详细信息，请参阅 [使用按钮](~/ios/tvos/user-interface/buttons.md) 文档。
- **清晰标记破坏性按钮** -对于执行破坏性操作的按钮 (如删除文件) 使用样式清楚地标记这些按钮 `UIAlertActionStyle.Destructive` 。

<a name="Displaying-an-Alert"></a>

## <a name="displaying-an-alert"></a>显示警报

若要显示警报，请创建的实例， `UIAlertViewController` 并通过将 "操作" (按钮添加) 并选择该警报的样式来进行配置。 例如，以下代码显示 "确定"/"取消" 警报：

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...

var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

让我们详细了解一下此代码。 首先，创建一个具有给定标题和消息的新警报：

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

接下来，对于我们要在警报中显示的每个按钮，我们将创建一个操作，用于定义按钮的标题、样式以及在按下按钮时要执行的操作：

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ =>
    // Do something when the button is pressed
    ...
);
```

`UIAlertActionStyle`枚举允许你将按钮样式设置为以下内容之一：

- **默认值** -显示警报时，该按钮将为选中状态。
- **取消** -该按钮是警报的 "取消" 按钮。
- **破坏性** -将按钮突出显示为破坏性操作，如删除文件。 目前，tvOS 呈现具有红色背景的破坏性按钮。

`AddAction`方法将给定操作添加到， `UIAlertViewController` 最后， `PresentViewController (alertController, true, null)` 方法向用户显示给定的警报。

<a name="Adding-Text-Fields"></a>

## <a name="adding-text-fields"></a>添加文本字段

除了将操作添加到警报)  (按钮外，还可以向警报添加文本字段，以允许用户填写用户 Id 和密码等信息：

[![警报中的文本字段](alerts-images/alert02.png)](alerts-images/alert02.png#lightbox)

如果用户选择文本字段，将显示标准的 tvOS 键盘，使其能够为字段输入值：

[![输入文本](alerts-images/alert03.png)](alerts-images/alert03.png#lightbox)

以下代码显示一个 "确定"/"取消" 警报，其中包含用于输入值的单个文本字段：

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

`AddTextField`方法会将一个新的文本字段添加到警报，然后通过设置属性（如占位符文本 (在字段为空时显示的文本) 、默认文本值和键盘类型）来进行配置。 例如：

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

这样，我们可以在以后使用文本字段的值，也可以使用以下代码保存的副本：

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

用户在 "文本" 字段中输入值后，可以使用该 `field` 变量访问该值。

<a name="Alert-View-Controller-Helper-Class"></a>

## <a name="alert-view-controller-helper-class"></a>警报视图控制器帮助程序类

由于使用显示简单的常见警报类型 `UIAlertViewController` 可能会导致很多重复的代码，因此，可以使用帮助器类来减少重复的代码量。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

使用此类，可以按如下所示显示和响应简单警报：

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍 `UIAlertController` 如何使用将警报消息显示给 tvOS 中的用户。 首先，它展示了如何显示简单的警报并添加按钮。 接下来，该示例演示如何向警报添加文本字段。 最后，它说明了如何使用 helper 类来减少显示警报所需的重复代码量。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)