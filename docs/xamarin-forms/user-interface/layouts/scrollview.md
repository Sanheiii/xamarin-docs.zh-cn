---
title: Xamarin.Forms ScrollView
description: Xamarin.FormsScrollView 是能够滚动其内容的布局。
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: af8513d86b7cf02ec7adcee75241d8ce99e2dfab
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556823"
---
# <a name="no-locxamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)

[![：： no (Xamarin。 Forms) ：：： ScrollView](scrollview-images/layouts.png "：： no (Xamarin。 Forms) ：：： ScrollView")](scrollview-images/layouts-large.png#lightbox "：： no (Xamarin。 Forms) ：：： ScrollView")

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 是能够滚动其内容的布局。 `ScrollView`类派生自 [`Layout`](xref:Xamarin.Forms.Layout) 类，默认情况下，它会垂直滚动其内容。 `ScrollView`只能有一个子级，尽管这可以是其他布局。

> [!WARNING]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView) 对象不应嵌套。 此外， `ScrollView` 对象不应与提供滚动的其他控件嵌套，例如 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 、 [`ListView`](xref:Xamarin.Forms.ListView) 和 [`WebView`](xref:Xamarin.Forms.WebView) 。

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定义以下属性：

- [`Content`](xref:Xamarin.Forms.ScrollView.Content)，类型为 [`View`](xref:Xamarin.Forms.View) ，表示要在中显示的内容 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。
- [`ContentSize`](xref:Xamarin.Forms.ScrollView)类型为的 [`Size`](xref:Xamarin.Forms.Size) 表示内容的大小。 这是只读属性。
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView)类型为的， [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) 表示水平滚动条可见的时间。
- [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation)，类型为 [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) ，表示的滚动方向 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。 此属性的默认值为 `Vertical`。
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollX)类型为的， `double` 指示当前 X 滚动位置。 此只读属性的默认值为0。
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollY)类型为的， `double` 指示当前的 Y 滚动位置。 此只读属性的默认值为0。
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView)类型为的， [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) 表示垂直滚动条可见的时间。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，但 [`Content`](xref:Xamarin.Forms.ScrollView.Content) 属性除外，这意味着它们可以是数据绑定的目标和样式。

[`Content`](xref:Xamarin.Forms.ScrollView.Content)属性是类的 [`ContentProperty`](xref:Xamarin.Forms.ContentPropertyAttribute) [`ScrollView`](xref:Xamarin.Forms.ScrollView) ，因此无需从 XAML 显式设置。

> [!TIP]
> 若要获得最佳布局性能，请遵循 [优化布局性能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的准则。

## <a name="scrollview-as-a-root-layout"></a>ScrollView 作为根布局

[`ScrollView`](xref:Xamarin.Forms.ScrollView)只能有一个子级，可以是其他布局。 因此 `ScrollView` ，通常将作为页面上的根布局。 若要滚动其子内容，需 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 计算其内容的高度与其自身高度之间的差异。 这种差异是指 `ScrollView` 可以滚动其内容的数量。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)通常是的子级 `ScrollView` 。 在这种情况下，将 `ScrollView` 导致的高度等于其子级的 `StackLayout` 高度之和。 然后， `ScrollView` 可以确定其内容可以滚动的量。 有关的详细信息 `StackLayout` ，请参阅[ Xamarin.Forms StackLayout](stacklayout.md)。

> [!CAUTION]
> 在垂直方向 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 下，避免将 `VerticalOptions` 属性设置为 `Start` 、 `Center` 或 `End` 。 这样做会告诉， `ScrollView` 只需尽可能高，这可能是零。 尽管可以 Xamarin.Forms 防范这种后果同样，但最好是避免不需要的代码。

下面的 XAML 示例将 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 作为页面上的根布局：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ScrollViewDemos"
             x:Class="ScrollViewDemos.Views.ColorListPage"
             Title="ScrollView demo">
    <ScrollView>
        <StackLayout BindableLayout.ItemsSource="{x:Static local:NamedColor.All}">
            <BindableLayout.ItemTemplate>
                <DataTemplate>
                    <StackLayout Orientation="Horizontal">
                        <BoxView Color="{Binding Color}"
                                 HeightRequest="32"
                                 WidthRequest="32"
                                 VerticalOptions="Center" />
                        <Label Text="{Binding FriendlyName}"
                               FontSize="24"
                               VerticalOptions="Center" />
                    </StackLayout>
                </DataTemplate>
            </BindableLayout.ItemTemplate>
        </StackLayout>
    </ScrollView>
</ContentPage>
```

在此示例中，将 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 其内容设置为 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，以使用可绑定的布局来显示 [`Color`](xref:Xamarin.Forms.Color) 由定义的字段 Xamarin.Forms 。 默认情况下， `ScrollView` 垂直滚动显示更多内容：

[![根 ScrollView 布局的屏幕截图](scrollview-images/root-layout.png "根 ScrollView 布局")](scrollview-images/root-layout-large.png#lightbox "根 ScrollView 布局")

等效 C# 代码如下：

```csharp
public class ColorListPageCode : ContentPage
{
    public ColorListPageCode()
    {
        DataTemplate dataTemplate = new DataTemplate(() =>
        {
            BoxView boxView = new BoxView
            {
                HeightRequest = 32,
                WidthRequest = 32,
                VerticalOptions = LayoutOptions.Center
            };
            boxView.SetBinding(BoxView.ColorProperty, "Color");

            Label label = new Label
            {
                FontSize = 24,
                VerticalOptions = LayoutOptions.Center
            };
            label.SetBinding(Label.TextProperty, "FriendlyName");

            StackLayout horizontalStackLayout = new StackLayout
            {
                Orientation = StackOrientation.Horizontal,
                Children = { boxView, label }
            };
            return horizontalStackLayout;
        });

        StackLayout stackLayout = new StackLayout();
        BindableLayout.SetItemsSource(stackLayout, NamedColor.All);
        BindableLayout.SetItemTemplate(stackLayout, dataTemplate);

        ScrollView scrollView = new ScrollView { Content = stackLayout };

        Title = "ScrollView demo";
        Content = scrollView;
    }
}
```

有关可绑定布局的详细信息，请参阅[中 Xamarin.Forms 的可绑定布局](bindable-layouts.md)。

## <a name="scrollview-as-a-child-layout"></a>作为子布局的 ScrollView

[`ScrollView`](xref:Xamarin.Forms.ScrollView)可以是不同父布局的子布局。

[`ScrollView`](xref:Xamarin.Forms.ScrollView)通常是的子级 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 `ScrollView`需要特定高度来计算其内容的高度与其自身的高度之间的差异，差别在于 `ScrollView` 可以滚动其内容的数量。 当 `ScrollView` 是的子级时 `StackLayout` ，它不会接收特定的高度。 `StackLayout`需要尽可能短的时间 `ScrollView` ，即内容的高度 `ScrollView` 或零。 若要处理这种情况， `VerticalOptions` 应将的属性 `ScrollView` 设置为 `FillAndExpand` 。 这将导致 `StackLayout` 为 `ScrollView` 其他子级提供不需要的所有额外空间，并 `ScrollView` 将具有特定的高度。

下面的 XAML 示例将 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 作为的子布局 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ScrollViewDemos.Views.BlackCatPage"
             Title="ScrollView as a child layout demo">
    <StackLayout Margin="20">
        <Label Text="THE BLACK CAT by Edgar Allan Poe"
               FontSize="Medium"
               FontAttributes="Bold"
               HorizontalOptions="Center" />
        <ScrollView VerticalOptions="FillAndExpand">
            <StackLayout>
                <Label Text="FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects." />
                <!-- More Label objects go here -->
            </StackLayout>
        </ScrollView>
    </StackLayout>
</ContentPage>
```

在此示例中，有两个 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 对象。 第一个 `StackLayout` 是根布局对象，它具有 [`Label`](xref:Xamarin.Forms.Label) 对象和 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 作为其子对象。 `ScrollView`具有 `StackLayout` `StackLayout` 包含多个对象的作为其内容 `Label` 。 这种安排可确保第一个 `Label` 始终处于屏幕上，而其他对象显示的文本 `Label` 可以滚动：

[![子 ScrollView 布局的屏幕截图](scrollview-images/child-layout.png "子 ScrollView 布局")](scrollview-images/child-layout-large.png#lightbox "子 ScrollView 布局")

等效 C# 代码如下：

```csharp
public class BlackCatPageCS : ContentPage
{
    public BlackCatPageCS()
    {
        Label titleLabel = new Label
        {
            Text = "THE BLACK CAT by Edgar Allan Poe",
            // More properties set here to define the Label appearance
        };

        ScrollView scrollView = new ScrollView
        {
            VerticalOptions = LayoutOptions.FillAndExpand,
            Content = new StackLayout
            {
                Children =
                {
                    new Label
                    {
                        Text = "FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects.",
                    },
                    // More Label objects go here
                }
            }
        };

        Title = "ScrollView as a child layout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { titleLabel, scrollView }
        };
    }
}
```

## <a name="orientation"></a>方向

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 具有一个 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 属性，该属性表示的滚动方向 `ScrollView` 。 此属性的类型为 [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) ，它定义以下成员：

- `Vertical` 指示 `ScrollView` 将垂直滚动。 此成员是属性的默认值 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 。
- `Horizontal` 指示 `ScrollView` 将水平滚动。
- `Both` 指示 `ScrollView` 将水平和垂直滚动。
- `Neither` 指示 `ScrollView` 不滚动。

> [!TIP]
> 可以通过将 [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) 属性设置为来禁用滚动 `Neither` 。

## <a name="detect-scrolling"></a>检测滚动

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定义一个 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) 事件，该事件将激发以指示发生滚动。 [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs)事件附带的对象 `Scrolled` 具有 `ScrollX` 和 `ScrollY` 属性，这两种类型均为 `double` 。

> [!IMPORTANT]
> `ScrolledEventArgs.ScrollX`和 `ScrolledEventArgs.ScrollY` 属性的值可以为负值，因为当滚动回开头时出现弹跳效果 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

下面的 XAML 示例显示了一个 [`ScrollView`](xref:Xamarin.Forms.ScrollView) ，它设置事件的事件处理程序 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) ：

```xaml
<ScrollView Scrolled="OnScrollViewScrolled">
        ...
</ScrollView>
```

等效 C# 代码如下：

```csharp
ScrollView scrollView = new ScrollView();
scrollView.Scrolled += OnScrollViewScrolled;
```

在此示例中，事件 `OnScrollViewScrolled` 处理程序在 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) 事件激发时执行：

```csharp
void OnScrollViewScrolled(object sender, ScrolledEventArgs e)
{
    Console.WriteLine($"ScrollX: {e.ScrollX}, ScrollY: {e.ScrollY}");
}
```

在此示例中， `OnScrollViewScrolled` 事件处理程序输出 [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) 事件随附的对象的值。

> [!NOTE]
> [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled)触发用户启动滚动的事件和以编程方式滚动事件。

## <a name="scroll-programmatically"></a>以编程方式滚动

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定义两个 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 方法，这些方法以异步方式滚动 `ScrollView` 。 某个重载滚动到中的指定位置 `ScrollView` ，而另一个重载将指定的元素滚动到视图中。 这两个重载都有一个额外的参数，可用于指示是否对滚动进行动画处理。

> [!IMPORTANT]
> [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*)当属性设置为时，方法不会导致滚动 [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) `Neither` 。

### <a name="scroll-a-position-into-view"></a>将位置滚动到视图

[`ScrollView`](xref:Xamarin.Forms.ScrollView)可以使用 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 接受 `double` `x` 和参数的方法滚动到中的位置 `y` 。 给定一个 `ScrollView` 名为的垂直对象 `scrollView` ，下面的示例演示如何从的顶部滚动到150与设备无关的单位 `ScrollView` ：

```csharp
await scrollView.ScrollToAsync(0, 150, true);
```

的第三个参数 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 是 `animated` 参数，它确定在以编程方式滚动时是否显示滚动动画 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

### <a name="scroll-an-element-into-view"></a>将元素滚动到视图

中的元素 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 可使用 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 接受和参数的方法滚动到视图中 [`Element`](xref:Xamarin.Forms.Element) [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 。 如果给定了一个名为的垂直 `ScrollView` 名称 `scrollView` 和 [`Label`](xref:Xamarin.Forms.Label) `label` ，则下面的示例演示如何将一个元素滚动到 view 中：

```csharp
await scrollView.ScrollToAsync(label, ScrollToPosition.End, true);
```

的第三个参数 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 是 `animated` 参数，它确定在以编程方式滚动时是否显示滚动动画 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

在将元素滚动到视图中时，可以使用方法的第二个参数来设置元素在滚动完成后的确切位置 `position` [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 。 此参数接受 [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 枚举成员：

- `MakeVisible` 指示应滚动元素，直到它在中可见 `ScrollView` 。
- `Start` 指示元素应滚动到的开头 `ScrollView` 。
- `Center` 指示元素应滚动到的中心 `ScrollView` 。
- `End` 指示元素应滚动到的末尾 `ScrollView` 。

## <a name="scroll-bar-visibility"></a>滚动条可见性

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定义 [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) 由可绑定属性支持的和属性。 这些属性可获取或设置一个 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) 枚举值，该值表示水平或垂直滚动条是否可见。 `ScrollBarVisibility` 枚举定义下列成员：

- `Default` 指示平台的默认滚动条行为，是和属性的默认值 `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` 。
- `Always` 指示滚动条是可见的，即使在视图中显示内容时也是如此。
- `Never` 指示即使内容无法在视图中显示，也不会显示滚动条。

## <a name="related-links"></a>相关链接

- [ScrollView 演示 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)
- [Xamarin.Forms StackLayout](stacklayout.md)
- [可绑定布局 Xamarin.Forms](bindable-layouts.md)