---
title: Xamarin 中的文本输入
description: 本文档介绍 Xamarin iOS 应用中的文本输入。 本文探讨了如何在 iOS 设计器中以编程方式使用 UITextField 和 UITextVIew。
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e8f8c2196edd5450ca2949d59c2fc06ff62274b7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435371"
---
# <a name="text-input-in-xamarinios"></a>Xamarin 中的文本输入

接受用户文本输入的目的是实现 `UITextField` 单行输入，并将 UITextView 用于多行可编辑文本。 您可以将其中任一控件拖动到屏幕上，然后双击以设置初始文本。

下面的屏幕截图显示了这些控件的图标，这些控件位于 Visual Studio for Mac 的 "工具箱" 面板中：

 [![UITextField](text-input-images/image11a.png)](text-input-images/image11a.png#lightbox)

 [![UITextView](text-input-images/image13a.png)](text-input-images/image13a.png#lightbox)

命名插座并保存情节提要文件后，Visual Studio for Mac 将更新 `.designer.cs` 分部类，您可以将引用控件的 c # 代码添加到您的类文件中。 每个控件都有其自己的唯一属性和可在 c # 代码中访问的事件。

 <a name="UITextField"></a>

## <a name="uitextfield"></a>UITextField

`UITextField`控件最常用于接受单行文本输入，例如用户名或密码。 下面显示了一些可用于自定义控件的选项：

 [![UITextField 属性](text-input-images/image15a.png)](text-input-images/image15a.png#lightbox)

这些控件如下所述：

- **占位符** –这是可选的。 如果设置此设置，则在文本字段为空时显示，通常向用户解释预期输入。
- "**清除" 按钮**–此项控制在 "文本" 字段中显示标准的 "清除" 按钮 (灰色 (圆圈) # A3，作为用户快速清除文本的方式。 它可以永久隐藏、永久可见或显示，具体取决于是否正在编辑该字段。
- "**最小字体大小**" 和 "**调整为合适**大小" –允许自动调整字号以容纳较长的文本并防止截断，但限制为不小于指定的大小。
- **大小写** –是否自动将单词、句子或所有输入进行大写。
- **更正** –是否启用拼写检查和建议。
- **键盘** –控制为输入显示的键盘样式，并因此可以在键盘上使用哪些键。 这包括数字板、手机 Pad、电子邮件和其他选项。
- **外观** –控制键盘的外观样式，并将为深色或浅色主题。
- **返回键** –更改返回键上的标签，以更好地反映要采取的操作。 受支持的值包括 "开始"、"联接"、"下一步"、"路由"
- **Secure** –标识是否屏蔽输入 (例如) 的密码输入。

如果 `textfield1` 已使用设计器将名为的 UITextField 添加到屏幕，则可在 c # 中设置或更改其属性，如下所示：

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin 在适当的情况下提供枚举，以便轻松地选择所需的设置，如 `UIKeyboardType` `UIReturnKeyType` 以上代码段中的和。

### <a name="display-text-programmatically"></a>以编程方式显示文本

如果你不想使用设计器设计屏幕，或者想要在运行时动态添加某些文本，可以在视图控制器的方法中以编程方式创建和显示 UITextField， `ViewDidLoad` 如下所示：

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView"></a>

## <a name="uitextview"></a>UITextView

`UITextView`控件可用于显示只读文本或接受多行文本输入。 它具有许多与 `UITextField` (（如大小写、更正等) ）相同的选项。

 [![UITextView 属性](text-input-images/image16a.png)](text-input-images/image16a.png#lightbox)

特定属性包括：

- **行为** -文本是可编辑的还是只读的。
- **检测** –检测到输入的数据并将其转换为可单击的元素，例如可以触发呼叫的电话号码、成为地图的链接的地址、在 Safari 中打开的 url，或在日历中成为事件的日期和时间。

如果已使用设计器将 UITextView 添加到屏幕，则可以设置或更改其属性，如下所示：

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```

## <a name="related-links"></a>相关链接

- [控制 (示例) ](/samples/xamarin/ios-samples/controls)