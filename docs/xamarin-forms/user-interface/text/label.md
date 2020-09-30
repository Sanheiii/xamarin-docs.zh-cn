---
title: Xamarin.Forms 标识
description: 本文介绍如何使用 Xamarin.Forms Label 类在应用程序中显示单行文本和多行文本。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 742cc1171901d5aa9b81cab31fd45258686c2085
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557577"
---
# <a name="no-locxamarinforms-label"></a>Xamarin.Forms 标识

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_显示文本 Xamarin.Forms_

[`Label`](xref:Xamarin.Forms.Label)视图用于显示文本，同时为单行和多行。 标签可以具有文本修饰和彩色文本，并使用自定义字体)  (系列、大小和选项。

## <a name="text-decorations"></a>文本修饰

可以 [`Label`](xref:Xamarin.Forms.Label) 通过将 `Label.TextDecorations` 属性设置为一个或多个枚举成员，将下划线和删除线文本修饰应用到实例 `TextDecorations` ：

- `None`
- `Underline`
- `Strikethrough`

下面的 XAML 示例演示如何设置 `Label.TextDecorations` 属性：

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

等效 C# 代码如下：

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

以下屏幕截图显示了 `TextDecorations` 应用于实例的枚举成员 [`Label`](xref:Xamarin.Forms.Label) ：

![带有文本修饰的标签](label-images/label-textdecorations.png)

> [!NOTE]
> 还可以对实例应用文本修饰 [`Span`](xref:Xamarin.Forms.Span) 。 有关类的详细信息 `Span` ，请参阅 [格式化文本](#formatted-text)。

## <a name="transform-text"></a>转换文本

[`Label`](xref:Xamarin.Forms.Entry) `Text` 通过将属性设置为枚举的值，可以转换存储在属性中的文本大小写 `TextTransform` `TextTransform` 。 此枚举具有四个值：

- `None` 指示不转换文本。
- `Default` 指示将使用平台的默认行为。 这是 `TextTransform` 属性的默认值。
- `Lowercase` 指示文本将转换为小写。
- `Uppercase` 指示文本将转换为大写。

下面的示例演示如何将文本转换为大写：

```xaml
<Label Text="This text will be displayed in uppercase."
       TextTransform="Uppercase" />
```

等效 C# 代码如下：

```csharp
Label label = new Label
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="character-spacing"></a>字符间距

可以 [`Label`](xref:Xamarin.Forms.Label) 通过将 `Label.CharacterSpacing` 属性设置为值，将字符间距应用到实例 `double` ：

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

等效 C# 代码如下：

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

结果是，中显示的文本中的字符 [`Label`](xref:Xamarin.Forms.Label) 是与 `CharacterSpacing` 设备无关的单元相隔的间距。

## <a name="new-lines"></a>新行

从 XAML 将中的文本强制插入新行有两种主要方法 [`Label`](xref:Xamarin.Forms.Label) ：

1. 使用 unicode 换行符，即 " &amp; #10;"。
1. 使用 *属性元素* 语法指定文本。

下面的代码演示了这两种方法的示例：

```xaml
<!-- Unicode line feed character -->
<Label Text="First line &#10; Second line" />

<!-- Property element syntax -->
<Label>
    <Label.Text>
        First line
        Second line
    </Label.Text>
</Label>
```

在 c # 中，可以使用 "\n" 字符将文本强制到新行：

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>颜色

可以通过可绑定属性将标签设置为使用自定义文本颜色 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 。

需要特别注意，以确保每个平台的颜色都可用。 由于每个平台对于文本和背景色都具有不同的默认值，因此，需要小心选取每个平台的默认值。

下面的 XAML 示例设置的文本颜色 `Label` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a green label." />
    </StackLayout>
</ContentPage>
```

等效 C# 代码如下：

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a green label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

以下屏幕截图显示了设置属性的结果 `TextColor` ：

![标签 TextColor 示例](label-images/textcolor.png)

有关颜色的详细信息，请参阅 [颜色](~/xamarin-forms/user-interface/colors.md)。

## <a name="fonts"></a>字体

有关在上指定字体的详细信息 `Label` ，请参阅 [字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="truncation-and-wrapping"></a>截断和换行

可以设置标签来处理无法按多种方式在一行上容纳的文本，该 `LineBreakMode` 属性由属性公开。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 是具有以下值的枚举：

- **HeadTruncation** &ndash; 截断文本的开头，并显示结束。
- **CharacterWrap** &ndash; 将文本换行到字符边界处的新行。
- **MiddleTruncation** &ndash; 显示文本的开头和结尾，中间用省略号替换。
- **NoWrap** &ndash; 不会自动换行，只显示一行中可容纳的最多文本。
- **TailTruncation** &ndash; 显示文本的开头，截断结束。
- **换行** &ndash; 在字边界处环绕文本。

## <a name="display-a-specific-number-of-lines"></a>显示特定行数

[`Label`](xref:Xamarin.Forms.Label)可以通过将属性设置为值来指定显示的行数 `Label.MaxLines` `int` ：

- 如果 `MaxLines` 为-1 （这是其默认值），则会将 `Label` 属性的值考虑 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 为仅显示一行（可能已截断）或包含所有文本的所有行。
- 如果 `MaxLines` 为0，则 `Label` 不会显示。
- 如果 `MaxLines` 为1，则结果与将 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 属性设置为、、 [`NoWrap`](xref:Xamarin.Forms.LineBreakMode) 或 [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode) 相同 [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode) [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode) 。 但是， `Label` 如果适用，将遵循 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 与省略号的位置有关的属性的值。
- 当 `MaxLines` 大于1时， `Label` 将显示多达指定的行数，同时遵从属性的值（ [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 如果适用）。 但是，如果将属性设置为，则将 `MaxLines` 属性设置为大于1的值将不起作用 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) [`NoWrap`](xref:Xamarin.Forms.LineBreakMode) 。

下面的 XAML 示例演示如何 `MaxLines` 在上设置属性 [`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

等效 C# 代码如下：

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

下面的屏幕截图显示将 `MaxLines` 属性设置为2的结果，当文本足够长，超过2行时：

![标签 MaxLines 示例](label-images/label-maxlines.png)

## <a name="display-html"></a>显示 HTML

[`Label`](xref:Xamarin.Forms.Label)类具有 `TextType` 属性，该属性确定 `Label` 实例是否应显示纯文本或 HTML 文本。 应将此属性设置为枚举的成员之一 `TextType` ：

- `Text` 指示 `Label` 将显示纯文本，并且为属性的默认值 `Label.TextType` 。
- `Html` 指示 `Label` 将显示 HTML 文本。

因此， [`Label`](xref:Xamarin.Forms.Label) 实例可以通过将 `Label.TextType` 属性设置为 `Html` ，并将属性设置 `Label.Text` 为 HTML 字符串来显示 html：

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

在上面的示例中，HTML 中的双引号字符必须使用符号进行转义 `\` 。

在 XAML 中，HTML 字符串可能因另外转义和符号而变得不可读 `<` `>` ：

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

另外，为了提高可读性，可以在节中内联 HTML `CDATA` ：

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

在此示例中， `Label.Text` 属性设置为在部分中内联的 HTML 字符串 `CDATA` 。 这 `Text` 是因为属性是类的 `ContentProperty` `Label` 。

以下屏幕截图显示了一个 [`Label`](xref:Xamarin.Forms.Label) 显示 HTML：

![在 iOS 和 Android 上显示 HTML 的标签的屏幕截图](label-images/label-html.png)

> [!IMPORTANT]
> 在中显示 HTML [`Label`](xref:Xamarin.Forms.Label) 仅限于底层平台支持的 html 标记。

## <a name="formatted-text"></a>带格式文本

标签公开一个 [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) 属性，该属性允许在同一视图中显示具有多种字体和颜色的文本。

`FormattedText`属性的类型为 [`FormattedString`](xref:Xamarin.Forms.FormattedString) ，它包含一个或多个 [`Span`](xref:Xamarin.Forms.Span) 实例，通过属性进行设置 [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) 。 以下 `Span` 属性可用于设置视觉外观：

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) –跨距背景的颜色。
- `CharacterSpacing`，属于 `double` 类型，是 `Span` 文本字符之间的间距。
- [`Font`](xref:Xamarin.Forms.Span.Font) –跨度中的文本的字体。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) –范围中文本的字体特性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) –范围内的文本的字体所属的字体系列。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) –范围中文本的字体大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) –范围中文本的颜色。 此属性已过时，并且已被属性替换 `TextColor` 。
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -要应用于范围默认行高的乘数。 有关详细信息，请参阅 [行高](#line-height)。
- [`Style`](xref:Xamarin.Forms.Span.Style)  –应用于范围的样式。
- [`Text`](xref:Xamarin.Forms.Span.Text) –范围的文本。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) –范围中文本的颜色。
- `TextDecorations` -要应用于范围中的文本的修饰。 有关详细信息，请参阅 [文本修饰](#text-decorations)。

[`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)、 [`Text`](xref:Xamarin.Forms.Span.Text) 和可 [`Text`](xref:Xamarin.Forms.Span.Text) 绑定属性的默认绑定模式为 [`OneWay`](xref:Xamarin.Forms.BindingMode) 。 有关此绑定模式的详细信息，请参阅[绑定模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)指南中[的默认绑定模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode)。

此外， [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) 属性可用于定义将响应上的笔势的笔势识别器的集合 [`Span`](xref:Xamarin.Forms.Span) 。

> [!NOTE]
> 不能在中显示 HTML [`Span`](xref:Xamarin.Forms.Span) 。

下面的 XAML 示例说明了 `FormattedText` 包含三个实例的属性 [`Span`](xref:Xamarin.Forms.Span) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label LineBreakMode="WordWrap">
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}">
                        <Span.GestureRecognizers>
                            <TapGestureRecognizer Command="{Binding TapCommand}" />
                        </Span.GestureRecognizers>
                    </Span>
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

等效 C# 代码如下：

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });

        var span = new Span { Text = "default, " };
        span.GestureRecognizers.Add(new TapGestureRecognizer { Command = new Command(async () => await DisplayAlert("Tapped", "This is a tapped Span.", "OK")) });
        formattedString.Spans.Add(span);
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!IMPORTANT]
> [`Text`](xref:Xamarin.Forms.Span.Text) `Span` 可以通过数据绑定设置的属性。 有关详细信息，请参阅[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

请注意， [`Span`](xref:Xamarin.Forms.Span) 还可以响应添加到跨距集合的任何笔势 [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) 。 例如，在 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) `Span` 上面的代码示例中，已将添加到第二个。 因此，点击此方法时， `Span` `TapGestureRecognizer` 将通过执行 `ICommand` 属性定义的进行响应 [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) 。 有关手势识别器的详细信息，请参阅[ Xamarin.Forms 手势](~/xamarin-forms/app-fundamentals/gestures/index.md)。

以下屏幕截图显示了将 `FormattedString` 属性设置为三个实例的结果 `Span` ：

![标签 FormattedText 示例](label-images/formattedtext.png)

## <a name="line-height"></a>行高

[`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) 可以通过将 [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 属性设置为或将属性设置 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 为值，自定义和的垂直高度 `double` 。 在 iOS 和 Android 上，这些值是原始行高的乘数，而通用 Windows 平台 (UWP) `Label.LineHeight` 属性值为标签字体大小的乘数。

> [!NOTE]
>
> - 在 iOS 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 和 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 属性会更改在一行上容纳的文本的行高，以及环绕多行的文本。
> - 在 Android 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 和 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 属性仅更改换行到多行的文本的行高。
> - 在 UWP 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 属性更改换行到多行的文本的行高， [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 属性不起作用。

下面的 XAML 示例演示如何 [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 在上设置属性 [`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

等效 C# 代码如下：

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

以下屏幕截图显示了将 [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 属性设置为1.8 的结果：

![标签 LineHeight 示例](label-images/label-lineheight.png)

下面的 XAML 示例演示如何 [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 在上设置属性 [`Span`](xref:Xamarin.Forms.Span) ：

```xaml
<Label LineBreakMode="WordWrap">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. "
                  LineHeight="1.8"/>
            <Span Text="Nullam feugiat sodales elit, et maximus nibh vulputate id."
                  LineHeight="1.8" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

等效 C# 代码如下：

```csharp
var formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. ",
  LineHeight = 1.8
});
formattedString.Spans.Add(new Span
{
  Text = "Nullam feugiat sodales elit, et maximus nibh vulputate id.",
  LineHeight = 1.8
});
var label = new Label
{
  FormattedText = formattedString,
  LineBreakMode = LineBreakMode.WordWrap
};
```

以下屏幕截图显示了将 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 属性设置为1.8 的结果：

![Span LineHeight 示例](label-images/span-lineheight.png)

## <a name="padding"></a>填充

填充表示元素及其子元素之间的空间，用于将元素与其自身内容分离。 可以 [`Label`](xref:Xamarin.Forms.Label) 通过将 `Label.Padding` 属性设置为值，将填充应用到实例 [`Thickness`](xref:Xamarin.Forms.Thickness) ：

```xaml
<Label Padding="10">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum" />
            <Span Text="dolor sit amet." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

等效 C# 代码如下：

```csharp
FormattedString formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum"
});
formattedString.Spans.Add(new Span
{
  Text = "dolor sit amet."
});
Label label = new Label
{
    FormattedText = formattedString,
    Padding = new Thickness(20)
};
```

> [!IMPORTANT]
> 在 iOS 上，当 [`Label`](xref:Xamarin.Forms.Label) 创建设置属性的时 `Padding` ，将应用填充，并且可以在以后更新填充值。 但是，如果 `Label` 创建不设置属性的，则 `Padding` 稍后尝试设置该属性将不起作用。
>
> 在 Android 和通用 Windows 平台上， `Padding` 当 `Label` 创建或更高版本时，可以指定属性值。

有关填充的详细信息，请参阅 [边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="hyperlinks"></a>超链接

和实例显示的 [`Label`](xref:Xamarin.Forms.Label) 文本 [`Span`](xref:Xamarin.Forms.Span) 可以使用以下方法转换为超链接：

1. 设置 `TextColor` 或的和 `TextDecoration` 属性 [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) 。
1. 将添加 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 到 [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) 或的集合 [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) ，其 [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) 属性绑定到 `ICommand` ，其 [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) 属性包含要打开的 URL。
1. 定义 `ICommand` 将由执行的 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 。
1. 编写将由执行的代码 `ICommand` 。

下面的代码示例摘自 [Hyperlink 演示](/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) 示例，它显示了 [`Label`](xref:Xamarin.Forms.Label) 其内容是从多个实例设置的 [`Span`](xref:Xamarin.Forms.Span) ：

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Alternatively, click " />
            <Span Text="here"
                  TextColor="Blue"
                  TextDecorations="Underline">
                <Span.GestureRecognizers>
                    <TapGestureRecognizer Command="{Binding TapCommand}"
                                          CommandParameter="https://docs.microsoft.com/xamarin/" />
                </Span.GestureRecognizers>
            </Span>
            <Span Text=" to view Xamarin documentation." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

在此示例中，第一个和第三个 [`Span`](xref:Xamarin.Forms.Span) 实例包含文本，第二个实例 `Span` 表示一个 tappable 超链接。 它的文本颜色设置为蓝色，并且具有下划线文本修饰。 这会创建超链接的外观，如以下屏幕截图所示：

[![超链接](label-images/hyperlinks-small.png "超链接")](label-images/hyperlinks-large.png#lightbox)

点击超链接时， [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 将通过执行 `ICommand` 其属性定义的来做出响应 [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) 。 此外，通过将属性指定的 URL [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) `ICommand` 作为参数传递给。

XAML 页的代码隐藏包含 `TapCommand` 实现：

```csharp
public partial class MainPage : ContentPage
{
    // Launcher.OpenAsync is provided by Xamarin.Essentials.
    public ICommand TapCommand => new Command<string>(async (url) => await Launcher.OpenAsync(url));

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }
}
```

`TapCommand`执行 `Launcher.OpenAsync` 方法，并将 [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) 属性值作为参数传递。 `Launcher.OpenAsync`方法由提供 Xamarin.Essentials ，并在 web 浏览器中打开该 URL。 因此，整体效果是，当在页面上点击超链接时，将显示一个 web 浏览器，并导航到与该超链接关联的 URL。

### <a name="creating-a-reusable-hyperlink-class"></a>创建可重用的超链接类

创建超链接的前一种方法需要在应用程序中每次需要超链接时编写重复代码。 不过， [`Label`](xref:Xamarin.Forms.Label) 和类都 [`Span`](xref:Xamarin.Forms.Span) 可以创建子类来创建 `HyperlinkLabel` 和 `HyperlinkSpan` 类，并在其中添加了手势识别器和文本格式代码。

下面的代码示例摘自 [Hyperlink 演示](/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) 示例，它显示了一个 `HyperlinkSpan` 类：

```csharp
public class HyperlinkSpan : Span
{
    public static readonly BindableProperty UrlProperty =
        BindableProperty.Create(nameof(Url), typeof(string), typeof(HyperlinkSpan), null);

    public string Url
    {
        get { return (string)GetValue(UrlProperty); }
        set { SetValue(UrlProperty, value); }
    }

    public HyperlinkSpan()
    {
        TextDecorations = TextDecorations.Underline;
        TextColor = Color.Blue;
        GestureRecognizers.Add(new TapGestureRecognizer
        {
            // Launcher.OpenAsync is provided by Xamarin.Essentials.
            Command = new Command(async () => await Launcher.OpenAsync(Url))
        });
    }
}
```

`HyperlinkSpan`类定义 `Url` 属性和关联的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，并且构造函数设置超链接的外观，以及在 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 点击超链接时将做出响应。 点击时 `HyperlinkSpan` ， `TapGestureRecognizer` 将通过执行方法来做出响应， `Launcher.OpenAsync` 以便 `Url` 在 web 浏览器中打开由属性指定的 URL。

`HyperlinkSpan`可以通过将类的实例添加到 XAML 来使用类：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:HyperlinkDemo"
             x:Class="HyperlinkDemo.MainPage">
    <StackLayout>
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Alternatively, click " />
                    <local:HyperlinkSpan Text="here"
                                         Url="https://docs.microsoft.com/appcenter/" />
                    <Span Text=" to view AppCenter documentation." />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

## <a name="styling-labels"></a>设置标签样式

前面几节介绍 [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) 了每个实例的设置和属性。 但是，可以将属性集分组为一个样式，该样式始终应用到一个或多个视图。 这可以提高代码的可读性，并使设计更改更易于实现。 有关详细信息，请参阅 [样式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相关链接

- [文本 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [超链接 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [创建移动应用 Xamarin.Forms ，第3章](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [标签 API](xref:Xamarin.Forms.Label)
- [跨 API](xref:Xamarin.Forms.Span)