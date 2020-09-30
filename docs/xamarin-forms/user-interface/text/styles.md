---
title: Xamarin.Forms 文本样式
description: 本文介绍如何在应用程序中设置文本的样式 Xamarin.Forms 。 样式可定义一次，可供多个视图使用，但样式只能与一种类型的视图一起使用。
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2c6898439da5869d79bc500643ca3cfc52c2e3f
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557551"
---
# <a name="no-locxamarinforms-text-styles"></a>Xamarin.Forms 文本样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_设置文本样式 Xamarin.Forms_

样式可用于调整标签、项和编辑器的外观。 样式可定义一次，可供多个视图使用，但样式只能与一种类型的视图一起使用。
可以为样式提供 `Key` ，并使用特定控件的属性有选择地应用样式 `Style` 。

## <a name="built-in-styles"></a>内置样式

Xamarin.Forms 为常见方案包括几种 [内置](xref:Xamarin.Forms.Device.Styles) 样式：

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

若要应用其中一种内置样式，请使用 `DynamicResource` 标记扩展来指定样式：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 c # 中，从以下各项中选择内置样式 `Device.Styles` ：

```csharp
label.Style = Device.Styles.TitleStyle;
```

![设备样式示例](styles-images/builtinstyles.png)

## <a name="custom-styles"></a>自定义样式

样式由资源库和 setter 组成，其中包含属性和属性将设置为的值。

在 c # 中，将包含红色文本大小为30的标签的自定义样式定义如下：

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

在 XAML 中：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

请注意， (包括) 中定义的所有样式的资源 `ContentPage.Resources` ，它是更熟悉的元素的同级 `ContentPage.Content` 元素。

![自定义样式示例](styles-images/customstyle.png)

## <a name="applying-styles"></a>应用样式

创建样式后，可将其应用于与其匹配的任何视图 `TargetType` 。

在 XAML 中，通过将自定义样式的 `Style` 属性与 `StaticResource` 引用所需样式的标记扩展一起提供，可将其应用到视图：

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

在 c # 中，可以直接将样式应用于视图，也可以在页的中添加和检索样式 `ResourceDictionary` 。 直接添加：

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

若要从页面中添加和检索 `ResourceDictionary` ：

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

内置样式的应用方式不同，因为它们需要响应辅助功能设置。 若要在 XAML 中应用内置样式，请 `DynamicResource` 使用标记扩展：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 c # 中，从以下各项中选择内置样式 `Device.Styles` ：

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>可访问性

存在内置样式，使其更易于遵循辅助功能首选项。 使用任何内置样式时，如果用户设置其辅助功能首选项，字体大小将自动增加。

请看下面的示例，其中包含已启用和禁用辅助功能设置的内置样式：

禁用：

![禁用了辅助功能的设备样式](styles-images/pre-access.png)

已启用：

![启用了辅助功能的设备样式](styles-images/post-access.png)

若要确保可访问性，请确保在应用中将内置样式用作任何文本相关样式的基础，并一致地使用样式。 请参阅 [样式](~/xamarin-forms/user-interface/styles/index.md) ，更详细地了解如何扩展和使用样式。

## <a name="related-links"></a>相关链接

- [创建移动应用 Xamarin.Forms ，第12章](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [文本 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [样式](xref:Xamarin.Forms.Style)