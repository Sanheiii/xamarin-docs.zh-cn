---
title: ListView 性能
description: 尽管 ListView 是用于显示数据的强大视图，但它存在一些限制。 本文介绍如何使用应用程序中的 ListView 来确保性能的最佳性能 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c010ac513cec8ac59927325dbe80ad1c8ac556d8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559995"
---
# <a name="listview-performance"></a>ListView 性能

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

编写移动应用程序时，性能非常重要。 用户需要顺畅的滚动和快速的加载时间。 如果无法满足用户的期望，将会对应用商店中的评级收费，或者，对于业务线应用程序，需要为组织的时间和金钱付费。

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 是用于显示数据的功能强大的视图，但它存在一些限制。 使用自定义单元时，尤其是当它们包含深层嵌套的视图层次结构或使用某些需要复杂度量的布局时，滚动性能可能会受到影响。 幸运的是，可以使用一些方法来避免性能降低。

## <a name="caching-strategy"></a>缓存策略

Listview 通常用于显示比屏幕容纳更多的数据。 例如，音乐应用可能有一个歌曲库，其中包含上千个条目。 为每个条目创建一项会浪费宝贵的内存，并且性能会下降。 经常创建和销毁行需要应用程序不断地实例化和清理对象，这也会导致性能下降。

为了节省内存， [`ListView`](xref:Xamarin.Forms.ListView) 每个平台的本机等效项具有用于重用行的内置功能。 只有屏幕上可见的单元格才会加载到内存中，并且 **内容** 将加载到现有单元中。 此模式可防止应用程序实例化数千个对象，从而节省了时间和内存。

Xamarin.Forms 允许 [`ListView`](xref:Xamarin.Forms.ListView) 通过枚举实现单元格重复 [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) ，此枚举具有以下值：

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> 通用 Windows 平台 (UWP) 忽略 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 缓存策略，因为它始终使用缓存来提高性能。 因此，默认情况下，它的行为类似 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 于应用缓存策略。

### <a name="retainelement"></a>RetainElement

[`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略指定 [`ListView`](xref:Xamarin.Forms.ListView) 将为列表中的每一项生成一个单元格，并且为默认 `ListView` 行为。 应在以下情况下使用它：

- 每个单元格有大量的绑定 (20-30 +) 。
- 单元模板会频繁更改。
- 测试表明 `RecycleElement` 缓存策略会导致执行速度降低。

使用 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 自定义单元时，必须识别缓存策略的后果。 对于每个创建的单元格，都需要运行任何单元格初始化代码，每秒可以多次。 在这种情况下，在页面上正常运行的布局技术（如使用多个嵌套 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 实例）在用户滚动时被实时设置并销毁时会变得性能瓶颈。

### <a name="recycleelement"></a>RecycleElement

[`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略指定 [`ListView`](xref:Xamarin.Forms.ListView) 将通过回收列表单元来尝试最大程度地减少内存占用量和执行速度。 此模式并非始终能改善性能，并应执行测试来确定是否有任何改进。 但这是首选选项，应在以下情况下使用：

- 每个单元格的绑定数小到中等。
- 每个单元格 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 都定义了所有单元数据。
- 每个单元格在很大程度上相似，单元模板不变。

在虚拟化期间，单元将更新其绑定上下文，因此，如果应用程序使用此模式，它必须确保正确处理绑定上下文更新。 单元格的所有数据都必须来自绑定上下文，否则可能会发生一致性错误。 可以通过使用数据绑定显示单元数据来避免此问题。 或者，应在重写中设置单元格数据， `OnBindingContextChanged` 而不是在自定义单元的构造函数中设置，如下面的代码示例所示：

```csharp
public class CustomCell : ViewCell
{
    Image image = null;
    
    public CustomCell ()
    {
        image = new Image();
        View = image;
    }
    
    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();
        
        var item = BindingContext as ImageItem;
        if (item != null) {
            image.Source = item.ImageUrl;
        }
    }
}
```

有关详细信息，请参阅 [绑定上下文更改](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes)。

在 iOS 和 Android 上，如果单元格使用自定义呈现器，则它们必须确保正确实现属性更改通知。 当重复使用单元格时，将在绑定上下文更新到可用单元格的事件时更改其属性值，并 `PropertyChanged` 引发事件。 有关详细信息，请参阅 [自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

#### <a name="recycleelement-with-a-datatemplateselector"></a>使用并重的 RecycleElement

当 [`ListView`](xref:Xamarin.Forms.ListView) 使用选择时 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ， [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 缓存策略不会缓存 `DataTemplate` 。 相反， `DataTemplate` 将为列表中的每个数据项选择一个。

> [!NOTE]
> [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略具有2.4 中引入的先决条件， Xamarin.Forms 当 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 要求选择 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 每个 `DataTemplate` 必须返回相同类型的时，该策略都是必需的 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 。 例如，假设有一个 [`ListView`](xref:Xamarin.Forms.ListView) `DataTemplateSelector` ，它可以返回 `MyDataTemplateA` 一个 (，其中返回 `MyDataTemplateA` `ViewCell`) 类型的 `MyViewCellA` ，或返回 `MyDataTemplateB` (，其中返回 `MyDataTemplateB` 类型为) 的 `ViewCell` `MyViewCellB` ; 当返回时， `MyDataTemplateA` 它必须返回， `MyViewCellA` 否则将引发异常。

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

[`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate)缓存策略建立在 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 缓存策略之上，通过另外确保当 [`ListView`](xref:Xamarin.Forms.ListView) 使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 来选择时 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ， `DataTemplate` 将按列表中的项的类型缓存。 因此，对于 `DataTemplate` 每个项目类型，将选择一次，而不是对每个项目实例进行一次选择。

> [!NOTE]
> [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate)缓存策略有一个先决条件， `DataTemplate` 由返回的返回的 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 必须使用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) 采用的构造函数 `Type` 。

### <a name="set-the-caching-strategy"></a>设置缓存策略

[`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)枚举值是使用 [`ListView`](xref:Xamarin.Forms.ListView) 构造函数重载指定的，如下面的代码示例所示：

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

在 XAML 中， `CachingStrategy` 按下面的 xaml 中所示设置属性：

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

此方法与在 c # 中的构造函数中设置缓存策略参数的效果相同。

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>在子类 ListView 中设置缓存策略

`CachingStrategy`由于上没有属性，因此在一个子类上设置 XAML 的属性 [`ListView`](xref:Xamarin.Forms.ListView) 将不会生成所需的行为 `CachingStrategy` `ListView` 。 此外，如果启用了 [XAMLC](~/xamarin-forms/xaml/xamlc.md) ，则将生成以下错误消息： **没有为 "CachingStrategy" 找到属性、可绑定属性或事件**

此问题的解决方法是在子类上指定接受参数的构造函数 [`ListView`](xref:Xamarin.Forms.ListView) [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) ，并将其传递给基类：

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

然后， [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) 可以使用以下语法从 XAML 指定枚举值 `x:Arguments` ：

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>ListView 性能建议

可通过许多方法来提高性能 `ListView` 。 以下建议可以提高 ListView 的性能

- 将 `ItemsSource` 属性绑定到 `IList<T>` 集合而不是 `IEnumerable<T>` 集合，因为 `IEnumerable<T>` 集合不支持随机访问。
- 使用内置单元 (如 `TextCell`  /  `SwitchCell` ) ，而不是 `ViewCell` 每次都可以。
- 使用较少元素。 例如，请考虑使用单个 `FormattedString` 标签而不是多个标签。
- `ListView` `TableView` 当显示非同源数据（即不同类型的数据）时，请将替换为。
- 限制使用 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 方法。 如果过度滥用，则会降低性能。
- 在 Android 上，请避免在 `ListView` 实例化后设置的行分隔符可见性或颜色，因为这样会导致性能大幅下降。
- 避免基于更改单元格布局 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 更改布局会产生大量的度量和初始化成本。
- 避免深度嵌套的布局层次结构。 使用  `AbsoluteLayout` 或  `Grid` 有助于减少嵌套。
- `LayoutOptions`除 `Fill` (`Fill` 是计算) 的最便宜。
- `ListView`出于以下原因，请避免将放在中 `ScrollView` ：
  - `ListView`实现自己的滚动。
  - `ListView`不会接收任何手势，因为它们将由父级处理 `ScrollView` 。
  - `ListView`可以显示通过列表的元素进行滚动的自定义标头和脚注，这可能会提供所用的功能 `ScrollView` 。 有关详细信息，请参阅 [页眉和页脚](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)。
- 如果你需要在单元格中提供特定的复杂设计，则请考虑使用自定义呈现器。

`AbsoluteLayout` 无需执行单个度量值即可执行布局，使其具有高性能。 如果 `AbsoluteLayout` 无法使用，请考虑 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 。 如果使用 `RelativeLayout` ，则直接传递约束比使用表达式 API 要快得多。 此方法的速度更快，因为表达式 API 使用 JIT，而在 iOS 上必须解释此树，这会降低。 表达式 API 适用于仅在初始布局和旋转时需要的页面布局，但在中，它会在 `ListView` 滚动过程中持续运行，从而影响性能。

为或其单元生成自定义呈现器 [`ListView`](xref:Xamarin.Forms.ListView) 是降低布局计算对滚动性能的影响的一种方法。 有关详细信息，请参阅 [自定义 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) 和 [自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

## <a name="related-links"></a>相关链接

- [自定义呈现器视图 (示例) ](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [自定义呈现器 ViewCell (示例) ](/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)