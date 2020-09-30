---
title: Xamarin.Forms 条目
description: 本文介绍如何使用 Xamarin.Forms Entry 类在应用程序中接受单行文本或密码输入。
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ab2f0880f694bd9e270385dd6e0256374ed76c43
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562218"
---
# <a name="no-locxamarinforms-entry"></a>Xamarin.Forms 条目

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) 用于单行文本输入。 与 `Entry` 视图一样， [`Editor`](xref:Xamarin.Forms.Editor) 支持多种键盘类型。 此外，还 `Entry` 可用作密码字段。

## <a name="set-and-read-text"></a>设置和读取文本

`Entry`与其他文本显示视图一样，会公开 [`Text`](xref:Xamarin.Forms.InputView.Text) 属性。 此属性可用于设置和读取显示的文本 `Entry` 。 下面的示例演示如何 `Text` 在 XAML 中设置属性：

```xaml
<Entry Text="I am an Entry" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

若要读取文本，请访问 `Text` c # 中的属性：

```csharp
var text = MyEntry.Text;
```

## <a name="set-placeholder-text"></a>设置占位符文本

[`Entry`](xref:Xamarin.Forms.Entry)当不存储用户输入时，可以将设置为显示占位符文本。 这是通过将属性设置 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 为来实现的 `string` ，它通常用于指示适用于的内容类型 `Entry` 。 此外，可以通过将属性设置为来控制占位符文本颜色 [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> `Entry`可以通过设置其属性来定义的宽度 `WidthRequest` 。 不依赖于 `Entry` 基于其属性值定义的的宽度 `Text` 。

## <a name="prevent-text-entry"></a>禁止文本输入

[`Entry`](xref:Xamarin.Forms.Entry)通过将 `IsReadOnly` 属性（其默认值 `false` ）设置为，可以防止用户修改中的文本 `true` ：

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly`属性不会改变的视觉外观 [`Entry`](xref:Xamarin.Forms.Entry) ，这与还会将 `IsEnabled` 的视觉外观改为灰色的属性不同 `Entry` 。

## <a name="transform-text"></a>转换文本

[`Entry`](xref:Xamarin.Forms.Entry) `Text` 通过将属性设置为枚举的值，可以转换存储在属性中的文本大小写 `TextTransform` `TextTransform` 。 此枚举具有四个值：

- `None` 指示不转换文本。
- `Default` 指示将使用平台的默认行为。 这是 `TextTransform` 属性的默认值。
- `Lowercase` 指示文本将转换为小写。
- `Uppercase` 指示文本将转换为大写。

下面的示例演示如何将文本转换为大写：

```xaml
<Entry Text="This text will be displayed in uppercase."
       TextTransform="Uppercase" />
```

等效 C# 代码如下：

```csharp
Entry entry = new Entry
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>限制输入长度

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)属性可用于限制允许的输入长度 [`Entry`](xref:Xamarin.Forms.Entry) 。 应将此属性设置为正整数：

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

如果 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 属性值为0，则表示不允许任何输入，值 `int.MaxValue` （这是的默认值） [`Entry`](xref:Xamarin.Forms.Entry) 表示对可以输入的字符数没有有效限制。

## <a name="character-spacing"></a>字符间距

[`Entry`](xref:Xamarin.Forms.Entry)通过将 `Entry.CharacterSpacing` 属性设置为值，可将字符间距应用于 `double` ：

```xaml
<Entry ...
       CharacterSpacing="10" />
```

等效 C# 代码如下：

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

结果是，中显示的文本中的字符 [`Entry`](xref:Xamarin.Forms.Entry) 是与 `CharacterSpacing` 设备无关的单元相隔的间距。

> [!NOTE]
> `CharacterSpacing`属性值应用于和属性显示的文本 `Text` `Placeholder` 。

## <a name="password-fields"></a>密码字段

`Entry` 提供 `IsPassword` 属性。 当 `IsPassword` 为时 `true` ，该字段的内容将显示为黑色圆圈：

在 XAML 中：

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Entry IsPassword 示例](entry-images/password.png)

占位符可以与 `Entry` 配置为密码字段的的实例一起使用：

在 XAML 中：

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![Entry IsPassword 和 Placeholder 示例](entry-images/passwordplaceholder.png)

## <a name="set-the-cursor-position-and-text-selection-length"></a>设置光标位置和文本选择长度

[`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition)属性可用于返回或设置将下一个字符插入属性中存储的字符串的位置 [`Text`](xref:Xamarin.Forms.InputView.Text) ：

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

此属性的默认值 [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) 为0，表示将在的开头处插入文本 `Entry` 。

此外， [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) 属性可用于返回或设置中选定文本的长度 `Entry` ：

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

此属性的默认值 [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) 为0，表示未选择任何文本。

## <a name="display-a-clear-button"></a>显示 "清除" 按钮

`ClearButtonVisibility`属性可用于控制是否 [`Entry`](xref:Xamarin.Forms.Entry) 显示 "清除" 按钮，该按钮使用户能够清除文本。 应将此属性设置为 `ClearButtonVisibility` 枚举成员：

- `Never` 指示将永远不会显示 "清除" 按钮。 这是 `Entry.ClearButtonVisibility` 属性的默认值。
- `WhileEditing` 指示将在中显示 "清除" 按钮 [`Entry`](xref:Xamarin.Forms.Entry) ，同时包含焦点和文本。

下面的示例演示如何在 XAML 中设置属性：

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

等效 C# 代码如下：

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

下面的屏幕截图显示了 [`Entry`](xref:Xamarin.Forms.Entry) 已启用 "清除" 按钮的：

![在 iOS 和 Android 上使用 "清除" 按钮的项的屏幕截图](entry-images/entry-clear-button.png)

## <a name="customize-the-keyboard"></a>自定义键盘

用户与交互时显示的键盘 [`Entry`](xref:Xamarin.Forms.Entry) 可通过属性以编程方式设置 [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 为类中的以下属性之一 [`Keyboard`](xref:Xamarin.Forms.Keyboard) ：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – 用于短信和表情符号有用的地方。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – 默认键盘。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – 输入电子邮件地址时使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – 输入数字时使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – 输入文本时使用，而无需指定任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – 输入电话号码时使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – 输入文本时使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – 用于输入文件路径和 Web 地址。

这可以通过以下操作在 XAML 中实现：

```xaml
<Entry Keyboard="Chat" />
```

等效 C# 代码如下：

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

每个键盘的示例可在我们的 [食谱](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) 存储库中找到。

[`Keyboard`](xref:Xamarin.Forms.Keyboard) 类还具有 [`Create`](xref:Xamarin.Forms.Keyboard.Create*) 工厂方法，可用于通过指定大小写、拼写检查和建议行为来自定义键盘。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 枚举值指定为方法的参数，并返回自定义的 `Keyboard`。 `KeyboardFlags` 枚举包含以下值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – 没有功能添加到键盘。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – 指示输入的每句话的第一个词的首字母将自动大写。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – 指示将对输入的文本执行拼写检查。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – 指示将对输入的文本执行单词自动完成。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – 指示每个词的首字母将自动大写。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) – 指示每个字符将自动大写。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) – 指示不会发生自动大写。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – 指示将对输入的文本执行拼写检查、单词自动完成和句子首字母大写。

以下 XAML 代码示例演示如何自定义默认 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 来执行单词自动完成并将输入的每个字符的首字母大写：

```xaml
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

等效 C# 代码如下：

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="customize-the-return-key"></a>自定义返回键

可以通过将属性设置为枚举的值来自定义软键盘上的 return 键的外观，该键盘 [`Entry`](xref:Xamarin.Forms.Entry) 通过将属性设置为枚举的值来自定义 [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) [`ReturnType`](xref:Xamarin.Forms.ReturnType) ：

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) –指示不需要任何特定的返回密钥，并且将使用平台默认值。
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) –指示 "完成" 返回键。
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) –表示 "中转" 返回键。
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) –表示 "下一步" 返回键。
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) –表示 "搜索" 返回键。
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) –表示 "Send" 返回键。

下面的 XAML 示例演示如何设置返回键：

```xaml
<Entry ReturnType="Send" />
```

等效 C# 代码如下：

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> 返回键的确切外观依赖于平台。 在 iOS 上，返回键是基于文本的按钮。 但是，在 Android 和通用 Windows 平台上，返回密钥是一个基于图标的按钮。

按下 return 键时，将 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 激发事件，并 `ICommand` 执行由属性指定的任何 [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) 。 此外， `object` 由属性指定的任何 [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) 都将作为参数传递给 `ICommand` 。 有关命令的详细信息，请参阅[命令接口](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="enable-and-disable-spell-checking"></a>启用和禁用拼写检查

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否启用拼写检查。 默认情况下，属性设置为 `true` 。 用户输入文本时，会显示拼写错误。

但对于某些文本输入方案（如输入用户名），拼写检查提供了消极的体验，应通过将 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 属性设置为来禁用它 `false` ：

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 当 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 属性设置为 `false` ，并且未使用自定义键盘时，将禁用本机拼写检查器。 但是，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 设置的禁用拼写检查（如 [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) ），则将 `IsSpellCheckEnabled` 忽略属性。 因此，不能使用属性对显式禁用该属性的启用拼写检查 `Keyboard` 。

## <a name="enable-and-disable-text-prediction"></a>启用和禁用文本预测

[`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)属性控制是否启用文本预测和自动文本更正。 默认情况下，属性设置为 `true` 。 当用户输入文本时，将显示 word 预测。

但是，对于某些文本输入方案，例如输入用户名，文本预测和自动文本更正将提供消极的体验，并应通过将 [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) 属性设置为来禁用它 `false` ：

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 如果将 [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) 属性设置为 `false` ，并且未使用自定义键盘，则会禁用文本预测和自动文本更正。 但是，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 设置了禁用文本预测的，则 `IsTextPredictionEnabled` 忽略该属性。 因此，不能使用属性来为显式禁用它的启用文本预测 `Keyboard` 。

## <a name="colors"></a>颜色

可以通过以下可绑定属性将条目设置为使用自定义背景和文本颜色：

- **TextColor** &ndash; 设置文本的颜色。
- **BackgroundColor** &ndash; 设置文本后面显示的颜色。

需要特别注意，以确保每个平台的颜色都可用。 由于每个平台对于文本和背景颜色都具有不同的默认值，因此，如果设置了默认值，则通常需要设置这两个平台。

使用以下代码设置项的文本颜色：

在 XAML 中：

```xaml
<Entry TextColor="Green" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![Entry TextColor 示例](entry-images/textcolor.png)

请注意，占位符不受指定的影响 `TextColor` 。

若要在 XAML 中设置背景色：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![Entry BackgroundColor 示例](entry-images/textbackgroundcolor.png)

请注意确保选择的背景和文本颜色在每个平台上都可用，而不会遮盖任何占位符文本。

## <a name="events-and-interactivity"></a>事件和交互性

条目公开了两个事件：

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)&ndash;当项中的文本更改时引发。 在更改前后提供文本。
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)&ndash;用户通过按键盘上的 return 键结束输入时引发。

> [!NOTE]
> [`VisualElement`](xref:Xamarin.Forms.VisualElement)从其继承的类 [`Entry`](xref:Xamarin.Forms.Entry) 也具有 [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) 和 [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) 事件。

### <a name="completed"></a>已完成

`Completed`事件用于响应与项的交互的完成。 `Completed` 当用户通过按键盘上的 return 键 (或按 UWP) 上的 Tab 键来结束输入时，将引发。 事件的处理程序是一个一般事件处理程序，该处理程序使用发送方和 `EventArgs` ：

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

已完成的事件可以在 XAML 中订阅：

```xaml
<Entry Completed="Entry_Completed" />
```

和 c #：

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

事件在 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 引发后，将 `ICommand` 执行由属性指定的任何， [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) 并使用 `object` 由 [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) 传递到的属性指定的 `ICommand` 。

### <a name="textchanged"></a>TextChanged

`TextChanged`事件用于响应字段内容中的更改。

`TextChanged` 当发生更改时，将引发 `Text` `Entry` 。 事件的处理程序使用的实例 `TextChangedEventArgs` 。 `TextChangedEventArgs``Entry` `Text` 通过 `OldTextValue` 和属性提供对的旧值和新值的访问权限 `NewTextValue` ：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

`TextChanged`可以在 XAML 中订阅事件：

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

和 c #：

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>相关链接

- [文本 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [输入 API](xref:Xamarin.Forms.Entry)