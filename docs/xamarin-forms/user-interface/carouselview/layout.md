---
title: Xamarin CarouselView 布局
description: 默认情况下，CarouselView 会水平显示其项。 但是，也可以使用垂直方向。
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
ms.openlocfilehash: 3242148fa97e6b3795b57b2fed86f3643a5ecdf6
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517423"
---
# <a name="xamarinforms-carouselview-layout"></a>Xamarin CarouselView 布局

![](~/media/shared/preview.png "This API is currently pre-release")

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定义用于控制布局的下列属性：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类型`LinearItemsLayout`为的指定要使用的布局。
- `PeekAreaInsets`类型[`Thickness`](xref:Xamarin.Forms.Thickness)为的指定使相邻项部分可见的程度。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

默认情况下， [`CarouselView`](xref:Xamarin.Forms.CarouselView)会在水平方向上显示其项。 屏幕上将显示一项，其中包含滑动手势，导致在项集合中向前和向后导航。 但是，也可以使用垂直方向。 这是因为[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)属性的类型`LinearItemsLayout`为，它继承自[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)类。 `ItemsLayout`类定义以下属性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)类型[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)为的指定添加添加项的方向[`CarouselView`](xref:Xamarin.Forms.CarouselView) 。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)类型[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)为的指定对齐点如何与项对齐。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)类型[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)为的指定滚动时对齐点的行为。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。 有关对齐点的详细信息，请参阅[Xamarin CollectionView 滚动](scrolling.md)指南中的 "[对齐点](scrolling.md#snap-points)"。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举定义以下成员：

- `Vertical`指示添加项[`CarouselView`](xref:Xamarin.Forms.CarouselView)时将垂直扩展。
- `Horizontal`指示添加项[`CarouselView`](xref:Xamarin.Forms.CarouselView)时将水平扩展。

`LinearItemsLayout`类[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)从类继承，并定义一个`ItemSpacing`类型`double`为的属性，该属性表示每个项周围的空白区域。 此属性的默认值为0，其值必须始终大于或等于0。 `LinearItemsLayout`类还定义静态`Vertical`和`Horizontal`成员。 这些成员可以分别用来创建垂直或水平列表。 或者，可以`LinearItemsLayout`创建一个对象，并将[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员指定为参数。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)使用本机布局引擎执行布局。

## <a name="horizontal-layout"></a>水平布局

默认情况下[`CarouselView`](xref:Xamarin.Forms.CarouselView) ，将水平显示其项。 因此，不需要将[`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout)属性设置为使用此布局：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

此外，也可以[`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout)通过将属性设置为`LinearItemsLayout`对象来实现此布局，并将`Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员指定为`Orientation`属性值：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

这会导致布局在添加新项时水平增长：

[![IOS 和 Android 上的 CarouselView 水平布局屏幕截图](layout-images/horizontal.png "CarouselView 水平布局")](layout-images/horizontal-large.png#lightbox "CarouselView 水平布局")

## <a name="vertical-layout"></a>垂直布局

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以通过将[`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout)属性设置为`LinearItemsLayout`对象，并将`Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)枚举成员指定为`Orientation`属性值，来垂直显示其项：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CarouselView.ItemsLayout>
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

这会导致在添加新项时布局垂直增长：

[![IOS 和 Android 上的 CarouselView 垂直布局屏幕截图](layout-images/vertical.png "CarouselView 垂直布局")](layout-images/vertical-large.png#lightbox "CarouselView 垂直布局")

## <a name="partially-visible-adjacent-items"></a>部分可见的相邻项

默认情况下[`CarouselView`](xref:Xamarin.Forms.CarouselView) ，一次显示全部项。 但是，可以通过将`PeekAreaInsets`属性设置为一个`Thickness`值来更改此行为，此值指定使相邻项部分可见的程度。 这有助于向用户指示有其他要查看的项。 以下 XAML 显示了设置此属性的示例：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    ...
</CarouselView>
```

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    PeekAreaInsets = new Thickness(100)
};
```

结果是在屏幕上部分公开相邻项：

[![IOS 和 Android 上包含部分可见的相邻项的 CollectionView 的屏幕截图](layout-images/peek-items.png "CarouselView 速览区域嵌入")](layout-images/peek-items-large.png#lightbox "CarouselView 峰值区域插入")

## <a name="item-spacing"></a>项间距

默认情况下，中的[`CarouselView`](xref:Xamarin.Forms.CarouselView)每个项之间没有空格。 可以通过在使用的项布局上`ItemSpacing`设置属性来更改此行为`CarouselView`。

如果将[`CarouselView`](xref:Xamarin.Forms.CarouselView)其[`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout)属性设置为`LinearItemsLayout`对象，则`LinearItemsLayout.ItemSpacing`可以将属性设置为表示项`double`之间的间距的值：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

> [!NOTE]
> `LinearItemsLayout.ItemSpacing`属性具有一个验证回叫集，它可确保属性的值始终大于或等于0。

等效 C# 代码如下：

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

此代码会生成垂直布局，其中各项之间的间距为20。

## <a name="dynamic-resizing-of-items"></a>动态调整项的大小

中的项[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以在运行时通过更改中的元素的与布局相关的属性[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，在运行时动态调整大小。 例如，下面的代码示例[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)更改[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`Image`](xref:Xamarin.Forms.Image)对象的和属性及其父`HeightRequest` [`Frame`](xref:Xamarin.Forms.Frame)对象的属性：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

此`OnImageTapped`事件处理程序是为响应正在点击[`Image`](xref:Xamarin.Forms.Image)的对象而执行的，它更改图像的尺寸（及其父级`Frame`），以便更轻松地查看：

[![在 iOS 和 Android 上动态调整项大小的 CarouselView 屏幕截图](layout-images/runtime-resizing.png "CarouselView 动态项大小调整")](layout-images/runtime-resizing-large.png#lightbox "CarouselView 动态项大小调整")

## <a name="right-to-left-layout"></a>从右到左布局

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以通过将其[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)属性设置为， [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)从右到左的流方向布局其内容。 但是，在`FlowDirection`理想情况下，应在页面或根布局上设置属性，这会使页面或根布局中的所有元素响应流方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CarouselViewDemos.Views.HorizontalTemplateLayoutRTLPage"
             Title="Horizontal layout (RTL FlowDirection)"
             FlowDirection="RightToLeft">    
    <CarouselView ItemsSource="{Binding Monkeys}">
        ...
    </CarouselView>
</ContentPage>
```

具有父级[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)的元素的默认值为[`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此，从[`CarouselView`](xref:Xamarin.Forms.CarouselView)继承`FlowDirection`属性值[`ContentPage`](xref:Xamarin.Forms.ContentPage)。

有关流方向的详细信息，请参阅[从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相关链接

- [CarouselView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [从右到左本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin CarouselView 滚动](scrolling.md)
