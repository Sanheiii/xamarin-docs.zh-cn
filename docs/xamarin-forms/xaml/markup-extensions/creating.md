---
title: 创建 XAML 标记扩展
description: 本文介绍如何定义你自己的自定义 Xamarin.Forms XAML 标记扩展。 XAML 标记扩展是实现 IMarkupExtension 或 IMarkupExtension 接口的类 <T> 。
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 43c8cd0dd7b50e3a5bfbd15d9858bd4502fedacc
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558773"
---
# <a name="creating-xaml-markup-extensions"></a>创建 XAML 标记扩展

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

在编程级别，XAML 标记扩展是实现 [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) 或接口的类 [`IMarkupExtension<T>`](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) 。 可以浏览 GitHub 存储库的[ **MarkupExtensions**目录](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)中所述的标准标记扩展的源代码 Xamarin.Forms 。

还可以通过从或派生来定义自己的自定义 XAML 标记扩展 `IMarkupExtension` `IMarkupExtension<T>` 。 如果标记扩展获取特定类型的值，则使用一般窗体。 下面是几个标记扩展的情况 Xamarin.Forms ：

- `TypeExtension` 派生自 `IMarkupExtension<Type>`
- `ArrayExtension` 派生自 `IMarkupExtension<Array>`
- `DynamicResourceExtension` 派生自 `IMarkupExtension<DynamicResource>`
- `BindingExtension` 派生自 `IMarkupExtension<BindingBase>`
- `ConstraintExpression` 派生自 `IMarkupExtension<Constraint>`

这两个 `IMarkupExtension` 接口仅定义了一个名为的方法 `ProvideValue` ：

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

由于 `IMarkupExtension<T>` 派生自 `IMarkupExtension` 并且 `new` 在上包含关键字 `ProvideValue` ，因此它包含这两种 `ProvideValue` 方法。

通常，XAML 标记扩展定义用于返回值的属性。  (明显的例外是 `NullExtension` ，在这种情况下 `ProvideValue` 只返回 `null` 。 ) `ProvideValue` 方法具有一个类型的参数 `IServiceProvider` ，本文稍后将对此进行讨论。

## <a name="a-markup-extension-for-specifying-color"></a>用于指定颜色的标记扩展

以下 XAML 标记扩展允许你 `Color` 使用色调、饱和度和发光度组件来构造值。 它为颜色的四个分量定义四个属性，包括已初始化为1的 alpha 分量。 类从派生 `IMarkupExtension<Color>` ，以指示 `Color` 返回值：

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

由于 `IMarkupExtension<T>` 派生自 `IMarkupExtension` ，因此该类必须包含两个 `ProvideValue` 方法，一个返回， `Color` 另一个返回 `object` ，但第二个方法可以只调用第一个方法。

" **HSL 颜色演示** " 页显示了 `HslColorExtension` 可在 XAML 文件中显示以指定的颜色的各种方式 `BoxView` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

请注意，当 `HslColorExtension` 是 XML 标记时，这四个属性被设置为属性，但当它出现在大括号之间时，这四个属性以逗号分隔，且不带引号。 、和的默认值为 `H` `S` `L` 0，的默认值 `A` 为1，因此，如果您希望它们设置为默认值，则可以省略这些属性。 最后一个示例显示一个示例，其中亮度为0，这通常会导致黑色，但 alpha 通道为0.5，因此它在页面的白色背景上呈灰色显示：

[![HSL 颜色演示](creating-images/hslcolordemo-small.png "HSL 颜色演示")](creating-images/hslcolordemo-large.png#lightbox "HSL 颜色演示")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>用于访问位图的标记扩展

的参数 `ProvideValue` 是一个实现接口的对象 [`IServiceProvider`](xref:System.IServiceProvider) ，该接口是在 .net 命名空间中定义的 `System` 。 此接口具有一个成员，该成员是一个名为的方法，它 `GetService` 具有 `Type` 参数。

`ImageResourceExtension`下面显示的类显示一个可能使用 `IServiceProvider` 和 `GetService` 来获取一个 `IXmlLineInfoProvider` 对象，该对象可以提供指示检测到特定错误的位置的行和字符信息。 在这种情况下，如果尚未设置属性，则会引发异常 `Source` ：

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;
        return ImageSource.FromResource(assemblyName + "." + Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` 当 XAML 文件需要访问作为嵌入资源存储在 .NET Standard 库项目中的图像文件时，将很有用。 它使用 `Source` 属性调用静态 `ImageSource.FromResource` 方法。 此方法需要完全限定的资源名称，该名称由程序集名称、文件夹名称和文件名分隔。 此方法的第二个参数 `ImageSource.FromResource` 提供程序集名称，并且仅对 UWP 上的发布版本是必需的。 无论如何，都 `ImageSource.FromResource` 必须从包含位图的程序集调用，这意味着此 XAML 资源扩展不能是外部库的一部分，除非图像也在该库中。 有关访问存储为嵌入资源的位图的详细信息，请 (参阅 [**嵌入的图像**](~/xamarin-forms/user-interface/images.md#embedded-images) 一文。 ) 

尽管 `ImageResourceExtension` 需要 `Source` 设置属性，但 `Source` 属性在属性中指定为类的 content 属性。 这意味着 `Source=` 可以省略大括号中的表达式部分。 在 " **图像资源演示** " 页中， `Image` 元素使用文件夹名称和文件名（用句点分隔）提取两个图像：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

下面是正在运行的程序：

[![图像资源演示](creating-images/imageresourcedemo-small.png "图像资源演示")](creating-images/imageresourcedemo-large.png#lightbox "图像资源演示")

## <a name="service-providers"></a>服务提供商

通过使用的 `IServiceProvider` 参数 `ProvideValue` ，xaml 标记扩展可以获取有关在其中使用它们的 xaml 文件的有用信息。 但若要 `IServiceProvider` 成功使用参数，您需要了解特定上下文中可用的服务类型。 了解此功能的最佳方式是在 GitHub 上的存储库中的[ **MarkupExtensions**文件夹](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)中学习现有 XAML 标记扩展的源代码 Xamarin.Forms 。 请注意，某些类型的服务是内部的 Xamarin.Forms 。

在某些 XAML 标记扩展中，此服务可能很有用：

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

`IProvideValueTarget`接口定义了两个属性： `TargetObject` 和 `TargetProperty` 。 在类中获取此信息时 `ImageResourceExtension` ， `TargetObject` 为， `Image` 并且是的属性的 `TargetProperty` `BindableProperty` 对象 `Source` `Image` 。 这是在其上设置了 XAML 标记扩展的属性。

`GetService`实参为的调用 `typeof(IProvideValueTarget)` 实际上返回一个类型为的对象 `SimpleValueTargetProvider` ，该对象在 `Xamarin.Forms.Xaml.Internals` 命名空间中定义。 如果将的返回值强制转换 `GetService` 为该类型，则还可以访问一个 `ParentObjects` 属性，该属性是一个包含 `Image` 元素、 `Grid` 父元素和 `ImageResourceDemoPage` 父级的数组 `Grid` 。

## <a name="conclusion"></a>结论

XAML 标记扩展在 XAML 中发挥着重要作用，因为它扩展了从多个源设置属性的功能。 此外，如果现有的 XAML 标记扩展不能准确提供所需的内容，也可以编写自己的扩展。

## <a name="related-links"></a>相关链接

- [标记扩展 (示例) ](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [书籍中的 XAML 标记扩展章节 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)