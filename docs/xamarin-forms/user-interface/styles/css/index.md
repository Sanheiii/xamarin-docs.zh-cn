---
title: Xamarin.Forms使用级联样式表 (CSS) 设置应用的样式
description: Xamarin.Forms 支持使用 (CSS) 级联样式表样式化视觉元素。
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 39a6dbcbd5e825c6630bfb60b61bf00a308fdc0e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562777"
---
# <a name="styling-no-locxamarinforms-apps-using-cascading-style-sheets-css"></a>Xamarin.Forms使用级联样式表 (CSS) 设置应用的样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin.Forms 支持使用 (CSS) 级联样式表样式化视觉元素。_

Xamarin.Forms 可使用 CSS 对应用程序进行样式设计。 样式表由规则列表组成，每个规则包含一个或多个选择器和一个声明块。 声明块包含用括号括起来的声明列表，每个声明都包含一个属性、一个冒号和一个值。 当块中有多个声明时，分号将插入为分隔符。 下面的代码示例演示了一些 Xamarin.Forms 兼容的 CSS：

```css
navigationpage {
    -xf-bar-background-color: lightgray;
}

^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

在中 Xamarin.Forms ，CSS 样式表在运行时进行分析和计算，而不是编译时，并且在使用时重新分析样式表。

> [!NOTE]
> 目前，无法通过 CSS 对 XAML 样式设置进行任何设置。 但 XAML 样式可用于对当前不支持的属性的 CSS 进行补充 Xamarin.Forms 。 有关 XAML 样式的详细信息，请参阅[使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

[MonkeyAppCSS](/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)示例演示如何使用 CSS 为简单应用程序进行样式，并在以下屏幕截图中所示：

[![具有 CSS 样式的 MonkeyApp 主页](css-images/MonkeyAppMainPage.png "具有 CSS 样式的 MonkeyApp 主页")](css-images/MonkeyAppMainPage-Large.png#lightbox "具有 CSS 样式的 MonkeyApp 主页")

[![具有 CSS 样式的 MonkeyApp 详细信息页](css-images/MonkeyAppDetailPage.png "具有 CSS 样式的 MonkeyApp 详细信息页")](css-images/MonkeyAppDetailPage-Large.png#lightbox "具有 CSS 样式的 MonkeyApp 详细信息页")

## <a name="consuming-a-style-sheet"></a>使用样式表

向解决方案添加样式表的过程如下所示：

1. 向 .NET Standard 库项目添加一个空的 CSS 文件。
1. 将 CSS 文件的生成操作设置为 **EmbeddedResource**。

### <a name="loading-a-style-sheet"></a>加载样式表

有多种方法可用于加载样式表。

> [!NOTE]
> 目前不能在运行时更改样式表并应用新样式表。

### <a name="xaml"></a>XAML

在将样式表添加到之前，可以使用类对其进行加载和分析 [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ：

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

[`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source)属性将样式表指定为相对于封闭 XAML 文件位置的 URI，如果 uri 以开头，则为相对于项目根 `/` 。

> [!WARNING]
> 如果 CSS 文件的生成操作未设置为  **EmbeddedResource**，则该文件将无法加载。

此外，还可以使用类加载和分析样式表， [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) 然后再将其添加到 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `CDATA` 部分：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

有关资源字典的详细信息，请参阅 [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

### <a name="c"></a>C\#

在 c # 中，可以从加载样式表， `StringReader` 并将其添加到中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ：

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

方法的参数 `StyleSheet.FromReader` 是 `TextReader` 已读取样式表的。

## <a name="selecting-elements-and-applying-properties"></a>选择元素和应用属性

CSS 使用选择器来确定要定位的元素。 具有匹配选择器的样式按定义顺序连续应用。 在特定项上定义的样式始终是最后应用的。 有关支持的选择器的详细信息，请参阅 [选择器引用](#selector-reference)。

CSS 使用属性对选定元素进行样式。 每个属性都具有一组可能的值，而某些属性可能会影响任何类型的元素，而另一些属性则应用于元素组。 有关支持的属性的详细信息，请参阅 [属性参考](#property-reference)。

如果子样式设置相同的属性，则它始终覆盖父样式表。 因此，在应用设置相同属性的样式时遵循下列优先规则：

- 如果在应用程序资源中定义的样式设置相同的属性，则它将被在页资源中定义的样式覆盖。
- 如果控件资源中定义的样式设置相同的属性，则在页资源中定义的样式将覆盖该样式。
- 如果在应用程序资源中定义的样式设置相同的属性，则该样式将被在控件资源中定义的样式覆盖。

> [!IMPORTANT]
> 不支持 CSS 变量。

### <a name="selecting-elements-by-type"></a>按类型选择元素

可视化树中的元素可以由类型选择，不区分大小写 `element` 选择器：

```css
stacklayout {
    margin: 20;
}
```

此选择器标识 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 页面上使用样式表的任何元素，并将其边距设置为统一的宽度（20）。

> [!NOTE]
> `element`选择器不标识指定类型的子类。

### <a name="selecting-elements-by-base-class"></a>按基类选择元素

使用不区分大小写选择器的基类可以选择可视化树中的元素 `^base` ：

```css
^contentpage {
    background-color: lightgray;
}
```

此选择器标识 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用样式表的任何元素，并将其背景色设置为 `lightgray` 。

> [!NOTE]
> `^base`选择器特定于 Xamarin.Forms ，而不是 CSS 规范的一部分。

### <a name="selecting-an-element-by-name"></a>按名称选择元素

可以通过区分大小写选择器选择可视化树中的单个元素 `#id` ：

```css
#listView {
    background-color: lightgray;
}
```

此选择器标识其 [`StyleId`](xref:Xamarin.Forms.Element.StyleId) 属性设置为的元素 `listView` 。 但是，如果 `StyleId` 未设置该属性，则选择器将回退到使用 `x:Name` 元素的。 因此，在下面的 XAML 示例中， `#listView` 选择器将标识 [`ListView`](xref:Xamarin.Forms.ListView) 其 `x:Name` 特性设置为的 `listView` ，并将其背景色设置为 `lightgray` 。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>选择具有特定类特性的元素

可以使用区分大小写选择器选择具有特定类属性的元素 `.class` ：

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

可以通过将 [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) 元素的属性设置为 css 类名称，将 CSS 类赋给 XAML 元素。 因此，在下面的 XAML 示例中，将类定义的样式 `.detailPageTitle` 分配给第一个 [`Label`](xref:Xamarin.Forms.Label) ，同时将类定义的样式 `.detailPageSubtitle` 分配给第二个 `Label` 。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>选择子元素

可视化树中的子元素可以用不区分大小写的 `element element` 选择器进行选择：

```css
listview image {
    height: 60;
    width: 60;
}
```

此选择器标识 [`Image`](xref:Xamarin.Forms.Image) 作为元素的子元素的所有元素 [`ListView`](xref:Xamarin.Forms.ListView) ，并将其高度和宽度设置为60。 因此，在下面的 XAML 示例中， `listview image` 选择器将标识为的 [`Image`](xref:Xamarin.Forms.Image) 子 [`ListView`](xref:Xamarin.Forms.ListView) ，并将其高度和宽度设置为60。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> `element element`选择器不需要子元素成为父元素的_直接_子级–子元素可能具有不同的父元素。 如果祖先是指定的第一个元素，则会发生选择。

### <a name="selecting-direct-child-elements"></a>选择直接子元素

可以在可视化树中选择直接子元素，不区分大小写 `element>element` 选择器：

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

此选择器标识 [`Image`](xref:Xamarin.Forms.Image) 作为元素的直接子元素的任何元素 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，并将其高度和宽度设置为200。 因此，在下面的 XAML 示例中， `stacklayout>image` 选择器将标识为的 [`Image`](xref:Xamarin.Forms.Image) 直接子项 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，并将其高度和宽度设置为200。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> `element>element`选择器要求子元素是父元素的_直接_子级。

## <a name="selector-reference"></a>选择器引用

支持以下 CSS 选择器 Xamarin.Forms ：

|选择器|示例|说明|
|---|---|---|
|`.class`|`.header`|选择 `StyleClass` 属性包含 "标头" 的所有元素。 请注意，此选择器区分大小写。|
|`#id`|`#email`|选择 `StyleId` 设置为的所有元素 `email` 。 如果 `StyleId` 未设置，则回退到 `x:Name` 。 使用 XAML 时， `x:Name` 优先于 `StyleId` 。 请注意，此选择器区分大小写。|
|`*`|`*`|选择所有元素。|
|`element`|`label`|选择类型的所有元素 `Label` ，但不选择子类。 请注意，此选择器不区分大小写。|
|`^base`|`^contentpage`|选择作为基类的所有元素 `ContentPage` ，包括 `ContentPage` 自身。 请注意，此选择器不区分大小写，并且不是 CSS 规范的组成部分。|
|`element,element`|`label,button`|选择所有 `Button` 元素和所有 `Label` 元素。 请注意，此选择器不区分大小写。|
|`element element`|`stacklayout label`|选择 `Label` 中的所有元素 `StackLayout` 。 请注意，此选择器不区分大小写。|
|`element>element`|`stacklayout>label`|选择 `Label` 作为直接父元素的所有元素 `StackLayout` 。 请注意，此选择器不区分大小写。|
|`element+element`|`label+entry`|直接选择 `Entry` 后面的所有元素 `Label` 。 请注意，此选择器不区分大小写。|
|`element~element`|`label~entry`|选择 `Entry` 前面有一个的所有元素 `Label` 。 请注意，此选择器不区分大小写。|

具有匹配选择器的样式按定义顺序连续应用。 在特定项上定义的样式始终是最后应用的。

> [!TIP]
> 选择器可以组合而不受限制，例如 `StackLayout>ContentView>label.email` 。

当前不支持以下选择器：

- `[attribute]`
- `@media` 和 `@supports`
- `:` 和 `::`

> [!NOTE]
> 不支持的特征重写。

## <a name="property-reference"></a>属性参考

"值" 列中的 (支持以下 CSS 属性 Xamarin.Forms ，类型为_斜体_，而字符串文本**Values** `gray`) ：

|属性|适用于|值|示例|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_颜色_ \|`initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_字符串_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_颜色_ \|`initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_double_ \|`initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_double_ \|`initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_颜色_ \|`initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_double_ \|`initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \|`auto` \| `initial`. 此外，可使用符号指定范围介于0% 到100% 之间的百分比 `%` 。|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_float_ \|`initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_float_ \|`initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_字符串_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_double_ \|_namedsize_ \|  `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_double_ \|`initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_double_ \|`initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_double_ \|`initial` |`line-height: 1.8;`|
|`margin`|`View`|_粗细_ \|`initial` |`margin: 6 12;`|
|`margin-left`|`View`|_粗细_ \|`initial` |`margin-left: 3;`|
|`margin-top`|`View`|_粗细_ \|`initial` |`margin-top: 2;`|
|`margin-right`|`View`|_粗细_ \|`initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_粗细_ \|`initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \|`initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_double_ \|`initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_double_ \|`initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_double_ \|`initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \|`initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_粗细_ \|`initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_double_ \|`initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _double_ \|`initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _double_ \|`initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left``right`在从右到左的环境中，应该避免。| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`text-transform`|`Button`,`Editor`, `Entry`, `Label`, `SearchBar`, `SearchHandler`|`none` \| `default` \| `uppercase` \| `lowercase` \| `initial` |`text-transform: uppercase;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _double_、 _double_ \|`initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_double_ \|`initial`|`min-width: 320;`|

> [!NOTE]
> `initial` 是所有属性的有效值。 它会清除值 (重置为从另一种样式设置的默认) 。

当前不支持以下属性：

- `all: initial`.
-  (框或网格) 的布局属性。
- 速记属性，如 `font` 、和 `border` 。

此外，没有任何值， `inherit` 因此不支持继承。 因此，您不能这样做，例如，在 `font-size` 布局上设置属性，并 [`Label`](xref:Xamarin.Forms.Label) 期望布局中的所有实例继承值。 一个例外是 `direction` 属性，该属性的默认值为 `inherit` 。

目标 `Span` 元素有一个已知问题，即使用符号)  (元素和名称，阻止其成为 CSS 样式的目标 `#` 。 `Span`元素派生自 `GestureElement` ，它不具有 `StyleClass` 属性，因此不支持 CSS 类定位。 有关详细信息，请参阅 [无法将 CSS 样式应用于跨控件](https://github.com/xamarin/Xamarin.Forms/issues/5979)。

### <a name="no-locxamarinforms-specific-properties"></a>Xamarin.Forms 特定属性

Xamarin.Forms还支持以下特定 CSS 属性 (在 "**值**" 列中，类型为_斜体_，而字符串文本 `gray`) ：

|属性|适用于|值|示例|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_颜色_ \|`initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_颜色_ \|`initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`, `SearchBar`|_int_ \|`initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_颜色_ \|`initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_颜色_ \|`initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` 仅在上受支持 `ScrollView` 。 |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_带引号文本_ \|`initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_颜色_ \|`initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_double_ \|`initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_颜色_ \|`initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_字符串_ \| `initial` |`-xf-visual: material;`|

### <a name="no-locxamarinforms-shell-specific-properties"></a>Xamarin.Forms Shell 特定属性

Xamarin.Forms还支持以下 Shell 特定 CSS 属性 (在 "**值**" 列中，类型为 "_斜体_"，而字符串文本 `gray`) ：

|属性|适用于|值|示例|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_颜色_ \|`initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_颜色_ \|`initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_颜色_ \|`initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_颜色_ \|`initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_颜色_ \|`initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_颜色_ \|`initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_颜色_ \|`initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_颜色_ \|`initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_颜色_ \|`initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_颜色_ \|`initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_颜色_ \|`initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>颜色

支持以下 `color` 值：

- `X11`[颜色](https://en.wikipedia.org/wiki/X11_color_names)与 CSS 颜色、UWP 预定义颜色和 Xamarin.Forms 颜色匹配。 请注意，这些颜色值不区分大小写。
- 十六进制颜色： `#rgb` 、 `#argb` 、 `#rrggbb` 、 `#aarrggbb`
- rgb 颜色： `rgb(255,0,0)` 、 `rgb(100%,0%,0%)` 。 值介于0-255 到 0%-100% 之间。
- rgba 颜色： `rgba(255, 0, 0, 0.8)` 、 `rgba(100%, 0%, 0%, 0.8)` 。 不透明度值介于 0.0-1.0 之间。
- hsl 颜色： `hsl(120, 100%, 50%)` 。 H 值的范围为0-360，而 s 和 l 介于 0%-100% 范围内。
- hsla 颜色： `hsla(120, 100%, 50%, .8)` 。 不透明度值介于 0.0-1.0 之间。

### <a name="thickness"></a>Thickness

支持一个、两个、三个或四个 `thickness` 值，每个值由空格分隔：

- 单个值指示均匀宽度。
- 两个值指示垂直和水平宽度。
- 三个值指示 top，然后水平 (左和右) ，然后靠下。
- 四个值指示 "上"、"右"、"下" 和 "左" 粗细。

> [!NOTE]
> CSS `thickness` 值与 XAML [`Thickness`](xref:Xamarin.Forms.Thickness) 值不同。 例如，在 XAML 中，两个值 `Thickness` 指示水平和垂直粗细，而四个值 `Thickness` 指示左、上、右、下宽度。 此外，XAML `Thickness` 值用逗号分隔。

### <a name="namedsize"></a>NamedSize

支持以下不区分大小写的 `namedsize` 值：

- `default`
- `micro`
- `small`
- `medium`
- `large`

每个值的确切含义 `namedsize` 取决于平台和视图相关。

## <a name="functions"></a>函数

可以 `linear-gradient()` 分别使用和 CSS 函数指定线性和径向渐变 `radial-gradient()` 。 应将这些函数的结果分配给控件的 `background` 属性。

## <a name="css-in-no-locxamarinforms-with-xamarinuniversity"></a>Xamarin.Forms用 Xamarin 大学提供的 CSS

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Forms 3.0 CSS 视频**

## <a name="related-links"></a>相关链接

- [MonkeyAppCSS (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)