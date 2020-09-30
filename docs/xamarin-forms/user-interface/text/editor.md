---
title: Xamarin.Forms编辑器
description: 本文介绍如何使用 Xamarin.Forms 编辑器控件在应用程序中接受多行文本输入。
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 42f89f09bd84127fd19bc3ab64794bdac7f145d7
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561620"
---
# <a name="no-locxamarinforms-editor"></a>Xamarin.Forms编辑器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

[`Editor`](xref:Xamarin.Forms.Editor)控件用于接受多行输入。

## <a name="set-and-read-text"></a>设置和读取文本

[`Editor`](xref:Xamarin.Forms.Editor)与其他文本显示视图一样，会公开 `Text` 属性。 此属性可用于设置和读取显示的文本 `Editor` 。 下面的示例演示如何 `Text` 在 XAML 中设置属性：

```xaml
<Editor Text="I am an Editor" />
```

在 C# 中：

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

若要读取文本，请访问 `Text` c # 中的属性：

```csharp
var text = MyEditor.Text;
```

## <a name="set-placeholder-text"></a>设置占位符文本

[`Editor`](xref:Xamarin.Forms.Editor)当不存储用户输入时，可以将设置为显示占位符文本。 这是通过将属性设置 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 为来实现的 `string` ，它通常用于指示适用于的内容类型 `Editor` 。 此外，可以通过将属性设置为来控制占位符文本颜色 [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

## <a name="prevent-text-entry"></a>禁止文本输入

[`Editor`](xref:Xamarin.Forms.Editor)通过将 `IsReadOnly` 属性（其默认值 `false` ）设置为，可以防止用户修改中的文本 `true` ：

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly`属性不会改变的视觉外观 [`Editor`](xref:Xamarin.Forms.Editor) ，这与还会将 `IsEnabled` 的视觉外观改为灰色的属性不同 `Editor` 。

## <a name="transform-text"></a>转换文本

[`Editor`](xref:Xamarin.Forms.Editor) `Text` 通过将属性设置为枚举的值，可以转换存储在属性中的文本大小写 `TextTransform` `TextTransform` 。 此枚举具有四个值：

- `None` 指示不转换文本。
- `Default` 指示将使用平台的默认行为。 这是 `TextTransform` 属性的默认值。
- `Lowercase` 指示文本将转换为小写。
- `Uppercase` 指示文本将转换为大写。

下面的示例演示如何将文本转换为大写：

```xaml
<Editor Text="This text will be displayed in uppercase."
        TextTransform="Uppercase" />
```

等效 C# 代码如下：

```csharp
Editor editor = new Editor
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>限制输入长度

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)属性可用于限制允许的输入长度 [`Editor`](xref:Xamarin.Forms.Editor) 。 应将此属性设置为正整数：

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

如果 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 属性值为0，则表示不允许任何输入，值 `int.MaxValue` （这是的默认值） [`Editor`](xref:Xamarin.Forms.Editor) 表示对可以输入的字符数没有有效限制。

## <a name="character-spacing"></a>字符间距

[`Editor`](xref:Xamarin.Forms.Editor)通过将 `Editor.CharacterSpacing` 属性设置为值，可将字符间距应用于 `double` ：

```xaml
<Editor ...
        CharacterSpacing="10" />
```

等效 C# 代码如下：

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

结果是，中显示的文本中的字符 [`Editor`](xref:Xamarin.Forms.Editor) 是与 `CharacterSpacing` 设备无关的单元相隔的间距。

> [!NOTE]
> `CharacterSpacing`属性值应用于和属性显示的文本 `Text` `Placeholder` 。

## <a name="auto-size-an-editor"></a>自动调整编辑器大小

[`Editor`](xref:Xamarin.Forms.Editor)可以通过将属性设置为来使其内容自动调整大小 [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) ，这是枚举的值 [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) 。 此枚举有两个值：

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) 指示禁用自动调整大小，并为默认值。
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) 指示启用自动调整大小。

这可以在代码中完成，如下所示：

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

启用自动调整大小后， [`Editor`](xref:Xamarin.Forms.Editor) 当用户用文本填充它时，的高度将增加，而当用户删除文本时，高度将减小。

> [!NOTE]
> [`Editor`](xref:Xamarin.Forms.Editor)如果已设置属性，则不会自动调整 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 。

## <a name="customize-the-keyboard"></a>自定义键盘

用户与交互时显示的键盘 [`Editor`](xref:Xamarin.Forms.Editor) 可通过属性以编程方式设置 [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 为类中的以下属性之一 [`Keyboard`](xref:Xamarin.Forms.Keyboard) ：

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
<Editor Keyboard="Chat" />
```

等效 C# 代码如下：

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
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
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

等效 C# 代码如下：

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="enable-and-disable-spell-checking"></a>启用和禁用拼写检查

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否启用拼写检查。 默认情况下，属性设置为 `true` 。 用户输入文本时，会显示拼写错误。

但对于某些文本输入方案（如输入用户名），拼写检查提供了消极的体验，因此应通过将 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 属性设置为来禁用它 `false` ：

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 当 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 属性设置为 `false` ，并且未使用自定义键盘时，将禁用本机拼写检查器。 但是，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 设置的禁用拼写检查（如 [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) ），则将 `IsSpellCheckEnabled` 忽略属性。 因此，不能使用属性对显式禁用该属性的启用拼写检查 `Keyboard` 。

## <a name="enable-and-disable-text-prediction"></a>启用和禁用文本预测

`IsTextPredictionEnabled`属性控制是否启用文本预测和自动文本更正。 默认情况下，属性设置为 `true` 。 当用户输入文本时，将显示 word 预测。

但是，对于某些文本输入方案，例如输入用户名，文本预测和自动文本更正将提供消极的体验，并应通过将 `IsTextPredictionEnabled` 属性设置为来禁用它 `false` ：

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 如果将 `IsTextPredictionEnabled` 属性设置为 `false` ，并且未使用自定义键盘，则会禁用文本预测和自动文本更正。 但是，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 设置了禁用文本预测的，则 `IsTextPredictionEnabled` 忽略该属性。 因此，不能使用属性来为显式禁用它的启用文本预测 `Keyboard` 。

## <a name="colors"></a>颜色

`Editor` 可以设置为通过属性使用自定义背景色 `BackgroundColor` 。 需要特别注意，以确保每个平台的颜色都可用。 由于每个平台都有不同的文本颜色默认设置，因此可能需要为每个平台设置自定义背景色。 有关优化每个平台的 UI 的详细信息，请参阅 [使用平台](~/xamarin-forms/platform/device.md) 调整程序。

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![带有 BackgroundColor 示例的编辑器](editor-images/textbackgroundcolor.png)

请确保选择的背景和文本颜色在每个平台上都可用，而不会遮盖任何占位符文本。

## <a name="events-and-interactivity"></a>事件和交互性

`Editor` 公开两个事件：

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; 在编辑器中的文本更改时引发。 在更改前后提供文本。
- [已完成](xref:Xamarin.Forms.Editor.Completed) &ndash; 用户通过按键盘上的 return 键结束输入时引发。

> [!NOTE]
> [`VisualElement`](xref:Xamarin.Forms.VisualElement)从其继承的类 [`Entry`](xref:Xamarin.Forms.Entry) 也具有 [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) 和 [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) 事件。

### <a name="completed"></a>已完成

`Completed`事件用于响应与的交互的完成 `Editor` 。 `Completed` 当用户通过输入键盘上的 return 键 (或按 UWP) 上的 Tab 键来结束输入时，将引发。 事件的处理程序是一个一般事件处理程序，该处理程序使用发送方和 `EventArgs` ：

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

已完成的事件可以在代码和 XAML 中订阅：

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

`TextChanged`事件用于响应字段内容中的更改。

`TextChanged` 当发生更改时，将引发 `Text` `Editor` 。 事件的处理程序使用的实例 `TextChangedEventArgs` 。 `TextChangedEventArgs``Editor` `Text` 通过 `OldTextValue` 和属性提供对的旧值和新值的访问权限 `NewTextValue` ：

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

已完成的事件可以在代码和 XAML 中订阅：

在代码中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

## <a name="related-links"></a>相关链接

- [文本 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [编辑器 API](xref:Xamarin.Forms.Editor)