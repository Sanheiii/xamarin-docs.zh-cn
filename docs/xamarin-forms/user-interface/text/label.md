---
title: Xamarin.Forms 标签
description: 本文介绍如何使用 Xamarin.Forms 标签类在应用程序中显示单个和多行文本。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 19cc24c3937921f4f4d95584e1cd656c37b4155f
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71105964"
---
# <a name="xamarinforms-label"></a>Xamarin.Forms 标签

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_在 Xamarin.Forms 中显示文本_

[ `Label` ](xref:Xamarin.Forms.Label)视图用于显示文本、 单个和多行。 标签可以具有文本修饰，彩色文本，并使用自定义字体 （系列、 大小和选项）。

## <a name="text-decorations"></a>文本修饰

下划线和删除线的文本修饰可应用于[ `Label` ](xref:Xamarin.Forms.Label)通过设置实例`Label.TextDecorations`属性设置为一个或多个`TextDecorations`枚举成员：

- `None`
- `Underline`
- `Strikethrough`

下面的 XAML 示例演示了如何设置`Label.TextDecorations`属性：

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

等效的 C# 代码是：

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

下面的屏幕截图演示`TextDecorations`应用于枚举成员[ `Label` ](xref:Xamarin.Forms.Label)实例：

![带有文本修饰的标签](label-images/label-textdecorations.png)

> [!NOTE]
> 此外可应用于的文本修饰[ `Span` ](xref:Xamarin.Forms.Span)实例。 有关详细信息`Span`类，请参阅[格式的文本](#Formatted_Text)。

## <a name="colors"></a>颜色

标签可以设置为使用通过可绑定的自定义文本颜色[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)属性。

特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的文本和背景颜色的默认值，将需要谨慎地选择适用于每个默认值。

下面的 XAML 示例设置的文本颜色`Label`:

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

等效的 C# 代码是：

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

以下屏幕截图显示的设置结果`TextColor`属性：

![标签 TextColor 示例](label-images/textcolor.png)

有关颜色的详细信息，请参阅[颜色](~/xamarin-forms/user-interface/colors.md)。

## <a name="fonts"></a>字体

有关详细信息，有关上指定字体`Label`，请参阅[字体](~/xamarin-forms/user-interface/text/fonts.md)。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截断和换行

可以设置标签来处理不适合在以下几种方式，通过公开的一行文本`LineBreakMode`属性。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 是一个枚举，使用以下值：

- **HeadTruncation** &ndash;将截断的文本，其中显示最终的开头。
- **CharacterWrap** &ndash;到新行上的文本进行换行字符边界处。
- **MiddleTruncation** &ndash;显示开头和末尾的文本，与通过省略号中间的替换。
- **NoWrap** &ndash;不换行文本，仅显示可以为任意数量的文本适合某个行。
- **TailTruncation** &ndash;显示文本，截断结束的开头。
- **换行**&ndash;使文本在单词边界处换行。

## <a name="displaying-a-specific-number-of-lines"></a>显示指定的行数

通过显示的行数[ `Label` ](xref:Xamarin.Forms.Label)可以指定通过设置`Label.MaxLines`属性设置为`int`值：

- 当`MaxLines`为 0，`Label`尊重的值[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)属性既可以显示一个行中，可能会截断或具有的所有文本的所有行。
- 当`MaxLines`为 1，结果等同于设置[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)属性设置为[ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode)， [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode)， [`MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode)，或[ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode)。 但是，`Label`将遵守的值[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)省略号，如果适用的位置方面的属性。
- 当`MaxLines`大于 1，`Label`指定数量的行，同时遵循的值将显示[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)省略号，如果适用的位置方面的属性。 但是，将设置`MaxLines`属性的值大于 1 如果不起作用[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)属性设置为[ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode)。

下面的 XAML 示例演示了如何设置`MaxLines`上的属性[ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

等效的 C# 代码是：

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

以下屏幕截图显示的设置结果`MaxLines`属性设置为 2，在文本时足够长，以占用 2 个以上的行：

![标签 MaxLines 示例](label-images/label-maxlines.png)

<a name="Formatted_Text" />

## <a name="formatted-text"></a>带格式文本

标签公开一个[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)属性，该属性允许在同一视图中显示具有多种字体和颜色的文本。

`FormattedText`属性属于类型[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)，其中包含一个或多个[ `Span` ](xref:Xamarin.Forms.Span)情况下，通过设置[ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans)属性. 以下`Span`属性可以用于设置可视外观：

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) -s p a n 背景的颜色。
- [`Font`](xref:Xamarin.Forms.Span.Font) – 在范围中的文本的字体。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) – 在范围中的文本的字体属性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – 属于该范围中的文本的字体的字体系列。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – 范围中文本的字体的大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) – 范围中文本的颜色。 此属性已过时，已由`TextColor`属性。
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -若要将应用于跨度的默认行高度的乘数。 有关详细信息，请参阅[行高](#line-height)。
- [`Style`](xref:Xamarin.Forms.Span.Style)–应用于范围的样式。
- [`Text`](xref:Xamarin.Forms.Span.Text) – 跨度的文本。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) – 范围中文本的颜色。
- `TextDecorations` -若要应用于范围中的文本的修饰。 有关详细信息，请参阅[文本修饰](#text-decorations)。

> [!NOTE]
> 、和可绑定属性的默认绑定模式为[`OneWay`。](xref:Xamarin.Forms.BindingMode) [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`Text`](xref:Xamarin.Forms.Span.Text) [`Text`](xref:Xamarin.Forms.Span.Text) 有关此绑定模式的详细信息，请参阅[绑定模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)指南中[的默认绑定模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode)。

此外， [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers)属性可以用于定义将在响应手势的手势识别器的集合[ `Span` ](xref:Xamarin.Forms.Span)。

下面的 XAML 示例演示`FormattedText`由三个属性[ `Span` ](xref:Xamarin.Forms.Span)实例：

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

等效的 C# 代码是：

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
> [ `Text` ](xref:Xamarin.Forms.Span.Text)属性的`Span`可以通过数据绑定设置。 有关详细信息，请参阅[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

请注意， [ `Span` ](xref:Xamarin.Forms.Span)还可以添加到 span 的任何手势响应[ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers)集合。 例如， [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)已添加到第二个`Span`在上面的代码示例。 因此，当这`Span`点击`TapGestureRecognizer`将通过执行响应`ICommand`由定义[ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command)属性。 有关手势识别器的详细信息，请参阅[Xamarin.Forms 手势](~/xamarin-forms/app-fundamentals/gestures/index.md)。

以下屏幕截图显示设置的结果`FormattedString`属性设置为三个`Span`实例：

![标签 FormattedText 示例](label-images/formattedtext.png)

## <a name="line-height"></a>行高

垂直高度[ `Label` ](xref:Xamarin.Forms.Label)和一个[ `Span` ](xref:Xamarin.Forms.Span)可以通过设置自定义[ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)属性或[ `Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)到`double`值。 IOS 和 Android 上这些值是乘数的原始行高度，并在通用 Windows 平台 (UWP)`Label.LineHeight`属性值是标签字体大小的倍数。

> [!NOTE]
>
> - 在 iOS 上， [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)并[ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)属性更改的单个行中，可以容纳的文本和换行到多个行的文本行高度。
> - 在 Android 上， [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)并[ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)属性只能更改换行到多个行的文本行高度。
> - 在 UWP 中， [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)属性更改的换行到多个行的文本行高度和[ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)属性不起作用。

下面的 XAML 示例演示了如何设置[ `LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)上的属性[ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

等效的 C# 代码是：

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

以下屏幕截图显示设置的结果[ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)属性设置为 1.8:

![标签 LineHeight 示例](label-images/label-lineheight.png)

下面的 XAML 示例演示了如何设置[ `LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)上的属性[ `Span` ](xref:Xamarin.Forms.Span):

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

等效的 C# 代码是：

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

以下屏幕截图显示设置的结果[ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)属性设置为 1.8:

![Span LineHeight 示例](label-images/span-lineheight.png)

## <a name="hyperlinks"></a>中超

[`Label`](xref:Xamarin.Forms.Label) [和`Span`](xref:Xamarin.Forms.Span)实例显示的文本可以使用以下方法转换为超链接：

1. [`Label`](xref:Xamarin.Forms.Label)设置或`TextColor` `TextDecoration`的和属性。 [`Span`](xref:Xamarin.Forms.Span)
1. [`Label`](xref:Xamarin.Forms.Label) [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)将添加[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) `ICommand`到或[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)的集合，其属性绑定到，其属性包含要打开的 URL。 [`Span`](xref:Xamarin.Forms.Span)
1. 定义将由执行的[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) `ICommand` 。
1. 编写将由`ICommand`执行的代码。

下面的代码示例摘自[Hyperlink 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)示例，它显示了其内容[`Label`](xref:Xamarin.Forms.Label)是从多个[`Span`](xref:Xamarin.Forms.Span)实例设置的：

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

在此示例中，第一个和[`Span`](xref:Xamarin.Forms.Span)第三个实例包含文本， `Span`第二个实例表示一个 tappable 超链接。 它的文本颜色设置为蓝色，并且具有下划线文本修饰。 这会创建超链接的外观，如以下屏幕截图所示：

[![超链接](label-images/hyperlinks-small.png "超链接")](label-images/hyperlinks-large.png#lightbox)

点击超链接时， [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)将通过`ICommand`执行其[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)属性定义的来做出响应。 此外，通过[`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)将属性指定的 URL `ICommand`作为参数传递给。

XAML 页的代码隐藏包含`TapCommand`实现：

```csharp
public partial class MainPage : ContentPage
{
    public ICommand TapCommand => new Command<string>(OpenBrowser);

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }

    void OpenBrowser(string url)
    {
        Device.OpenUri(new Uri(url));
    }
}
```

执行方法，并将[`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)属性值作为参数传递。 `OpenBrowser` `TapCommand` 反过来，此方法会调用[`Device.OpenUri`](xref:Xamarin.Forms.Device.OpenUri*)方法以在 web 浏览器中打开 URL。 因此，整体效果是，当在页面上点击超链接时，将显示一个 web 浏览器，并导航到与该超链接关联的 URL。

### <a name="creating-a-reusable-hyperlink-class"></a>创建可重用的超链接类

创建超链接的前一种方法需要在应用程序中每次需要超链接时编写重复代码。 不过， [`Label`](xref:Xamarin.Forms.Label)和[`Span`](xref:Xamarin.Forms.Span)类都可以创建子类来创建`HyperlinkLabel`和`HyperlinkSpan`类，并在其中添加了手势识别器和文本格式代码。

下面的代码示例摘自[Hyperlink 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)示例，它显示了一个`HyperlinkSpan`类：

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
            Command = new Command(() => Device.OpenUri(new Uri(Url)))
        });
    }
}
```

类定义属性和关联[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)的，并且构造函数设置超链接的外观，以及[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)在点击超链接时将做出响应。 `Url` `HyperlinkSpan` 点击时`Url` ， `TapGestureRecognizer`将通过执行[方法来做出响应，以便在web浏览器中打开由属性指定的URL。`Device.OpenUri`](xref:Xamarin.Forms.Device.OpenUri*) `HyperlinkSpan`

可以通过将类的实例添加到 XAML 来使用类：`HyperlinkSpan`

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

## <a name="styling-labels"></a>样式标签

前面几节介绍设置[ `Label` ](xref:Xamarin.Forms.Label)并[ `Span` ](xref:Xamarin.Forms.Span)根据每个实例的属性。 但是，属性集可以分组为一致地应用于一个或多个视图的一种样式。 这可以提高代码的可读性，并轻松地实现设计更改。 有关详细信息，请参阅[样式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相关链接

- [文本 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [超链接（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [借助 Xamarin.Forms，第 3 章创建移动应用](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [标签 API](xref:Xamarin.Forms.Label)
- [跨度 API](xref:Xamarin.Forms.Span)
