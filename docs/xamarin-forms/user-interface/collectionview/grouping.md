---
title: Xamarin.Forms CollectionView 分组
description: CollectionView 可以通过将其 IsGrouped 属性设置为 true 来显示正确分组的数据。
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 51bdcc6f9a95911c548269dbf7b3050cc5921183
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556615"
---
# <a name="no-locxamarinforms-collectionview-grouping"></a>Xamarin.Forms CollectionView 分组

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

经常滚动列表中的大型数据集可能会变得难以使用。 在这种情况下，将数据组织成组可以更轻松地导航数据，从而改善用户体验。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 支持显示分组数据，并定义以下属性，这些属性控制其显示方式：

- `IsGrouped`类型为的， `bool` 指示是否应在组中显示基础数据。 此属性的默认值为 `false`。
- `GroupHeaderTemplate`，类型为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，要用于每个组的标头的模板。
- `GroupFooterTemplate`，类型为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，要用于每个组的脚注的模板。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着属性可以是数据绑定的目标。

以下屏幕截图显示了一个 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 显示分组数据的：

[![IOS 和 Android 上的 CollectionView 中的分组数据的屏幕截图](grouping-images/grouped-data.png "带有分组数据的 CollectionView")](grouping-images/grouped-data-large.png#lightbox "带有分组数据的 CollectionView")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="group-data"></a>组数据

必须先对数据进行分组，然后才能显示。 这可以通过创建组列表来实现，其中每个组都是项的列表。 组列表应为 `IEnumerable<T>` 集合，其中 `T` 定义了两个数据片段：

- 组名称。
- `IEnumerable`定义属于组的项的集合。

因此，对数据进行分组的过程是：

- 创建对单个项建模的类型。
- 创建一个为一组项建模的类型。
- 创建 `IEnumerable<T>` 集合，其中 `T` 是对一组项进行建模的类型。 因此，此集合为存储分组数据的组的集合。
- 向 `IEnumerable<T>` 集合中添加数据。

### <a name="example"></a>示例

在对数据进行分组时，第一步是创建一个为单个项建模的类型。 下面的示例演示 `Animal` 示例应用程序中的类：

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

`Animal`类对单个项建模。 然后就可以创建一组项的类型。 下面的示例演示 `AnimalGroup` 示例应用程序中的类：

```csharp
public class AnimalGroup : List<Animal>
{
    public string Name { get; private set; }

    public AnimalGroup(string name, List<Animal> animals) : base(animals)
    {
        Name = name;
    }
}
```

`AnimalGroup`类从 `List<T>` 类继承，并添加一个 `Name` 表示组名称的属性。

`IEnumerable<T>`然后，可以创建组的集合：

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

此代码定义一个名为的集合 `Animals` ，其中集合中的每一项都是一个 `AnimalGroup` 对象。 每个 `AnimalGroup` 对象都包含一个名称和一个 `List<Animal>` 定义 `Animal` 组中的对象的集合。

然后，可以将分组的数据添加到 `Animals` 集合中：

```csharp
Animals.Add(new AnimalGroup("Bears", new List<Animal>
{
    new Animal
    {
        Name = "American Black Bear",
        Location = "North America",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/0/08/01_Schwarzbär.jpg"
    },
    new Animal
    {
        Name = "Asian Black Bear",
        Location = "Asia",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG/180px-Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG"
    },
    // ...
}));

Animals.Add(new AnimalGroup("Monkeys", new List<Animal>
{
    new Animal
    {
        Name = "Baboon",
        Location = "Africa & Asia",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

此代码在集合中创建两个组 `Animals` 。 第一个 `AnimalGroup` 命名为 `Bears` ，并包含一 `List<Animal>` 组熊详细信息。 第二个 `AnimalGroup` 命名为 `Monkeys` ，包含 `List<Animal>` 猴子详细信息的集合。

## <a name="display-grouped-data"></a>显示分组数据

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 通过将属性设置为，可显示分组数据，前提是数据已正确分组 `IsGrouped` `true` ：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                ...
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

[`CollectionView`](xref:Xamarin.Forms.CollectionView)通过将属性设置为，可以定义中每个项的外观 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 有关详细信息，请参阅 [定义项外观](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)。

> [!NOTE]
> 默认情况下， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 将在组标头和表尾中显示组名。 可以通过自定义组头和组尾来更改此行为。

## <a name="customize-the-group-header"></a>自定义组标头

可以通过将属性设置为来自定义每个组标头的外观 `CollectionView.GroupHeaderTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupHeaderTemplate>
        <DataTemplate>
            <Label Text="{Binding Name}"
                   BackgroundColor="LightGray"
                   FontSize="Large"
                   FontAttributes="Bold" />
        </DataTemplate>
    </CollectionView.GroupHeaderTemplate>
</CollectionView>
```

在此示例中，每个组标头设置为 [`Label`](xref:Xamarin.Forms.Label) ，其中显示组名称，并设置了其他外观属性。 以下屏幕截图显示了自定义的组标头：

[![IOS 和 Android 上 CollectionView 中的自定义组标头的屏幕截图](grouping-images/customized-header.png "带有自定义组标头的 CollectionView")](grouping-images/customized-header-large.png#lightbox "带有自定义组标头的 CollectionView")

## <a name="customize-the-group-footer"></a>自定义组尾

可以通过将属性设置为来自定义每个组脚注的外观 `CollectionView.GroupFooterTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupFooterTemplate>
        <DataTemplate>
            <Label Text="{Binding Count, StringFormat='Total animals: {0:D}'}"
                   Margin="0,0,0,10" />
        </DataTemplate>
    </CollectionView.GroupFooterTemplate>
</CollectionView>
```

在此示例中，每个组脚注均设置为 [`Label`](xref:Xamarin.Forms.Label) ，以显示组中的项数。 以下屏幕截图显示了自定义的组脚注：

[![IOS 和 Android 上的 CollectionView 中的自定义组脚注的屏幕截图](grouping-images/customized-footer.png "带有自定义组脚注的 CollectionView")](grouping-images/customized-footer-large.png#lightbox "带有自定义组脚注的 CollectionView")

## <a name="empty-groups"></a>空组

当 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 显示分组的数据时，它会显示任何空组。 此类组将连同组头和表尾一起显示，这表示该组为空。 以下屏幕截图显示空组：

[![IOS 和 Android 上的 CollectionView 中的空组的屏幕截图](grouping-images/empty-group.png "带有空组的 CollectionView")](grouping-images/empty-group-large.png#lightbox "带有空组的 CollectionView")

> [!NOTE]
> 在 iOS 10 和更低版本中，空组的组头和组尾可能会全部显示在顶部 `CollectionView` 。

## <a name="group-without-templates"></a>无模板组

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以显示正确的分组数据，而不将 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

在此方案中，可以通过重写 `ToString` 对单个项建模的类型中的方法和对一组项建模的类型来显示有意义的数据。

## <a name="related-links"></a>相关链接

- [CollectionView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)