---
title: Xamarin.Forms 资源字典
description: Xamarin.Forms XAML 资源是可在整个应用程序中共享和重用的对象 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: cdf34d78751345857e89c6bfa6862b58207ad01e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562127"
---
# <a name="no-locxamarinforms-resource-dictionaries"></a>Xamarin.Forms 资源字典

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)是应用程序使用的资源的存储库 Xamarin.Forms 。 存储在中的典型资源 `ResourceDictionary` 包括 [样式](~/xamarin-forms/user-interface/styles/index.md)、 [控件模板](~/xamarin-forms/app-fundamentals/templates/control-template.md)、 [数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)、颜色和转换器。

在 XAML 中， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可以使用 `StaticResource` 或标记扩展引用存储在中的资源，并将其应用于元素 `DynamicResource` 。 在 c # 中，还可以在中定义资源， `ResourceDictionary` 然后使用基于字符串的索引器对其进行引用并将其应用于元素。 但是，在 c # 中使用是非常有利的， `ResourceDictionary` 因为可以将共享对象存储为字段或属性并直接访问，而无需首先从字典中检索它们。

## <a name="create-resources-in-xaml"></a>在 XAML 中创建资源

每个 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 派生的对象都有一个 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 属性，该属性 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可以包含资源。 同样， [`Application`](xref:Xamarin.Forms.Application) 派生对象具有 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 属性，该属性 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 可以包含资源。

Xamarin.Forms应用程序仅包含从派生的类 [`Application`](xref:Xamarin.Forms.Application) ，但通常使用派生自的许多类 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，包括页面、布局和控件。 这些对象中的任何对象都可以 `Resources` 将其属性设置为 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 包含资源。 选择在何处 `ResourceDictionary` 应对可使用资源的特定影响：

- `ResourceDictionary`附加到视图（如或）的中的资源 [`Button`](xref:Xamarin.Forms.Button) [`Label`](xref:Xamarin.Forms.Label) 只能应用于该特定对象。
- 附加到布局的中的资源 `ResourceDictionary` （如 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 或） [`Grid`](xref:Xamarin.Forms.Grid) 可应用于布局和该布局的所有子级。
- 在页面级别定义的中的资源 `ResourceDictionary` 可以应用于页面及其所有子项。
- 可以在应用程序中 `ResourceDictionary` 应用在应用程序级别定义的中的资源。

除了隐式样式以外，资源字典中的每个资源都必须具有使用特性定义的唯一字符串键 `x:Key` 。

以下 XAML 显示了 app.xaml 文件的应用程序级别中定义的资源 `ResourceDictionary` ： **App.xaml**

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.App">
    <Application.Resources>

        <Thickness x:Key="PageMargin">20</Thickness>

        <!-- Colors -->
        <Color x:Key="AppBackgroundColor">AliceBlue</Color>
        <Color x:Key="NavigationBarColor">#1976D2</Color>
        <Color x:Key="NavigationBarTextColor">White</Color>
        <Color x:Key="NormalTextColor">Black</Color>

        <!-- Implicit styles -->
        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{StaticResource NavigationBarColor}" />
            <Setter Property="BarTextColor"
                    Value="{StaticResource NavigationBarTextColor}" />
        </Style>

        <Style TargetType="{x:Type ContentPage}"
               ApplyToDerivedTypes="True">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>

    </Application.Resources>
</Application>
```

在此示例中，资源字典定义一个 [`Thickness`](xref:Xamarin.Forms.Thickness) 资源、多个 [`Color`](xref:Xamarin.Forms.Color) 资源和两个隐式 [`Style`](xref:Xamarin.Forms.Style) 资源。 有关类的详细信息 `App` ，请参阅[ Xamarin.Forms App class](~/xamarin-forms/app-fundamentals/application-class.md)。

> [!NOTE]
> 在显式标记之间放置所有资源也是有效的 `ResourceDictionary` 。 但是，因为 Xamarin.Forms 3.0，所以 `ResourceDictionary` 不需要标记。 相反，将 `ResourceDictionary` 自动创建对象，您可以在属性元素标记之间直接插入资源 `Resources` 。

## <a name="consume-resources-in-xaml"></a>在 XAML 中使用资源

每个资源都有一个使用特性指定的密钥 `x:Key` ，该属性将成为中的字典键 `ResourceDictionary` 。 该键用于引用 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 具有 [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) 或标记扩展的中的资源 [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) 。

`StaticResource`标记扩展类似于 `DynamicResource` 中的标记扩展，因为这两种方法都使用字典键来引用资源字典中的值。 但是，当 `StaticResource` 标记扩展执行单个字典查找时， `DynamicResource` 标记扩展将保留指向字典键的链接。 因此，如果替换与该键关联的字典条目，则更改将应用于该视觉对象。 这样就可以在应用程序中进行运行时资源更改。 有关标记扩展的详细信息，请参阅 [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)。

下面的 XAML 示例演示如何使用资源，并在中定义其他资源 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.HomePage"
             Title="Home Page">
    <StackLayout Margin="{StaticResource PageMargin}">
        <StackLayout.Resources>
            <!-- Implicit style -->
            <Style TargetType="Button">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </StackLayout.Resources>

        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries." />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked" />
    </StackLayout>
</ContentPage>
```

在此示例中， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象使用在应用程序级别资源字典中定义的隐式样式。 [`StackLayout`](xref:Xamarin.Forms.StackLayout)对象使用 `PageMargin` 在应用程序级别资源字典中定义的资源，而 [`Button`](xref:Xamarin.Forms.Button) 对象使用资源字典中定义的隐式样式 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 这会导致如以下屏幕截图中所示的外观：

[![使用 ResourceDictionary 资源](resource-dictionaries-images/consuming.png "使用 ResourceDictionary 资源")](resource-dictionaries-images/consuming-large.png#lightbox "使用 ResourceDictionary 资源")

> [!IMPORTANT]
> 特定于单个页面的资源不应包含在应用程序级别的资源字典中，因为这样将在应用程序启动时（而不是在页面要求时）分析此类资源。 有关详细信息，请参阅 [降低应用程序资源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="resource-lookup-behavior"></a>资源查找行为

当使用 [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) 或标记扩展引用资源时，将发生以下查找过程 [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) ：

- 对于用于设置属性的元素，在资源字典中检查所请求的密钥（如果存在）。 如果找到请求的密钥，则返回其值，并且查找进程将终止。
- 如果找不到匹配项，查找过程会向上搜索可视化树，检查每个父元素的资源字典。 如果找到请求的密钥，则返回其值，并且查找进程将终止。 否则，该进程将一直持续，直到到达根元素。
- 如果在根元素上找不到匹配项，则检查应用程序级别资源字典。
- 如果仍未找到匹配项， `XamlParseException` 则会引发。

因此，当 XAML 分析器遇到 `StaticResource` 或 `DynamicResource` 标记扩展时，它将通过使用它找到的第一个匹配项向上遍历可视化树来搜索匹配的键。 如果此搜索在页中结束，但仍未找到该键，则 XAML 分析器会搜索 `ResourceDictionary` 附加到对象的 `App` 。 如果仍未找到该键，则会引发异常。

## <a name="override-resources"></a>替代资源

资源共享密钥时，在可视化树中定义的资源将优先于定义较高的资源。 例如， `AppBackgroundColor` `AliceBlue` 在应用程序级别将资源设置为将由设置为的页级别资源重写 `AppBackgroundColor` `Teal` 。 同样，页级别 `AppBackgroundColor` 资源将被控件级别资源重写 `AppBackgroundColor` 。

## <a name="stand-alone-resource-dictionaries"></a>独立资源字典

派生自的类 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 也可以在单独的独立文件中。 然后，可以在应用程序之间共享结果文件。

若要创建此类文件，请将新的 **内容视图** 或 **内容页** 项添加到项目 (，但不添加 **内容视图** 或内容 **页** ，) 只使用 c # 文件。 删除代码隐藏文件，在 XAML 文件中，将基类的名称从 [`ContentView`](xref:Xamarin.Forms.ContentView) 或更改 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 为 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 此外，请 `x:Class` 从文件的根标记中删除该属性。

下面的 XAML 示例显示了一个 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 名为 **MyResourceDictionary**的：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}"
                       TextColor="{StaticResource NormalTextColor}"
                       FontAttributes="Bold" />
                <Label Grid.Column="1"
                       Text="{Binding Age}"
                       TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2"
                       Text="{Binding Location}"
                       TextColor="{StaticResource NormalTextColor}"
                       HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

在此示例中， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 包含单个资源，这是类型的对象 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 **MyResourceDictionary** 可通过将其合并到另一个资源字典中来使用。

## <a name="merged-resource-dictionaries"></a>合并的资源字典

合并资源字典将一个或多个 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 对象合并到另一个对象 `ResourceDictionary` 。

### <a name="merge-local-resource-dictionaries"></a>合并本地资源字典

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)可以 `ResourceDictionary` 通过创建一个对象，将该 `ResourceDictionary` 对象的 [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) 属性设置为 XAML 文件的文件名，并将其合并到另一个文件中：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <!-- Add more resources here -->
        <ResourceDictionary Source="MyResourceDictionary.xaml" />
        <!-- Add more resources here -->
    </ContentPage.Resources>
    ...
</ContentPage>
```

此语法不会实例化 `MyResourceDictionary` 类。 相反，它引用 XAML 文件。 出于此原因，在设置 [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) 属性时，不需要代码隐藏文件，并且 `x:Class` 可以从 **MyResourceDictionary** 文件的根标记中删除该属性。

> [!IMPORTANT]
> [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source)属性只能从 XAML 设置。

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>从其他程序集合并资源字典

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)还可以 `ResourceDictionary` 通过将添加到的属性中来合并到另一个 [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) `ResourceDictionary` 。 此方法允许合并资源字典，而不考虑它们所在的程序集。 如果从外部程序集合并资源字典，则需要将 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 生成操作设置为 **EmbeddedResource**，以创建代码隐藏文件，并 `x:Class` 在文件的根标记中定义特性。

> [!WARNING]
> [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)类还定义 [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) 属性。 但是，此属性已弃用，不应再使用。

下面的代码示例演示了向页面级别的集合中添加了两个资源字典 [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo"
             xmlns:theme="clr-namespace:MyThemes;assembly=MyThemes">
    <ContentPage.Resources>
        <ResourceDictionary>
            <!-- Add more resources here -->
            <ResourceDictionary.MergedDictionaries>
                <!-- Add more resource dictionaries here -->
                <local:MyResourceDictionary />
                <theme:LightTheme />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
            <!-- Add more resources here -->
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

在此示例中，同一程序集中的资源字典和外部程序集的资源字典合并到页面级别资源字典中。 此外，还可以在 `ResourceDictionary` 属性元素标记中添加其他对象 [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) 以及这些标记之外的其他资源。

> [!IMPORTANT]
> 中只能有一个 `MergedDictionaries` 属性元素标记 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，但可以根据需要将任意数量的对象放入其中 `ResourceDictionary` 。

当合并 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 的资源共享相同 `x:Key` 的属性值时， Xamarin.Forms 将使用以下资源优先级：

1. 资源字典的本地资源。
1. 资源字典中包含的资源已通过 `MergedDictionaries` 集合合并，并以相反的顺序在属性中列出 `MergedDictionaries` 。

> [!NOTE]
> 如果应用程序包含多个大型资源字典，则搜索资源字典可能是一项计算密集型任务。 因此，为了避免不必要的搜索，您应确保应用程序中的每个页面只使用适合页面的资源字典。

## <a name="related-links"></a>相关链接

- [资源字典 (示例) ](/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 样式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]