---
title: Xamarin.FormsFlexLayout
description: 使用 FlexLayout 来堆积或包装子视图的集合。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 88f89e3d17c693c48176a45605b036f56599ee6d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563622"
---
# <a name="the-no-locxamarinforms-flexlayout"></a>Xamarin.FormsFlexLayout

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_使用 FlexLayout 来堆积或包装子视图的集合。_

Xamarin.Forms [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 是版本3.0 中的新增 Xamarin.Forms 版本。 它基于 "CSS [挠性方框布局" 模块](https://www.w3.org/TR/css-flexbox-1/)（通常称为 " _弹性布局_ " 或 " _弹性_"），因为它包含多个灵活的选项来排列布局中的子级。

`FlexLayout` 类似于 Xamarin.Forms [`StackLayout`](~/xamarin-forms/user-interface/layouts/stacklayout.md) ，它可以在堆栈中水平和垂直排列子元素。 但是， `FlexLayout` 如果在单个行或列中容纳太多项，还可以包装其子级，还可以选择多个用于方向、对齐和适应各种屏幕大小的选项。

`FlexLayout` 派生自 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 并继承 [`Children`](xref:Xamarin.Forms.Layout`1.Children) 类型的属性 `IList<View>` 。

`FlexLayout` 定义六个公共可绑定属性和五个附加的可绑定属性，这些属性影响其子元素的大小、方向和对齐方式。  (如果你不熟悉附加的可绑定属性，请参阅 **[附加属性](~/xamarin-forms/xaml/attached-properties.md)** 一文。 ) 这些属性将详细介绍中 **[的可绑定属性](#the-bindable-properties-in-detail)** 和 **[附加的可绑定属性](#the-attached-bindable-properties-in-detail)**。 不过，本文首先介绍的一些 **[常见使用方案](#common-usage-scenarios)** ， `FlexLayout` 这些方案更通俗地说明了其中的许多属性。 本文末尾介绍了如何合并 `FlexLayout` [CSS 样式表](~/xamarin-forms/user-interface/styles/css/index.md)。

## <a name="common-usage-scenarios"></a>常见使用方案

**[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例程序包含多个页面，用于演示的一些常见用法 `FlexLayout` ，并使你可以使用其属性进行试验。

### <a name="using-flexlayout-for-a-simple-stack"></a>将 FlexLayout 用于简单堆栈

" **简单堆栈** " 页显示如何 `FlexLayout` 用 `StackLayout` 更简单的标记替代但更简单的标记。 在 XAML 页中定义了此示例中的所有内容。 `FlexLayout`包含四个子元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

在 iOS、Android 和通用 Windows 平台上运行的页面如下：

[!["简单堆栈" 页](flex-layout-images/SimpleStack.png ""简单堆栈" 页")](flex-layout-images/SimpleStack-Large.png#lightbox)

`FlexLayout` **SimpleStackPage**文件中显示了的三个属性：

- [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction)属性设置为枚举的值 [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) 。 默认值为 `Row`。 如果将属性设置为， `Column` 则会 `FlexLayout` 在单个项列中排列的子级。

    如果中的项 `FlexLayout` 排列在列中， `FlexLayout` 则称其为垂直 _主轴_ 和水平 _交叉轴_。

- [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems)属性的类型为 [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) ，指定如何在十字轴上对齐项。 `Center`选项会使每个项在水平居中。

    如果你使用的 `StackLayout` 是而不是 `FlexLayout` 此任务的，则可以通过将 `HorizontalOptions` 每个项的属性分配给来使所有项居中 `Center` 。 `HorizontalOptions`属性对的子级不起作用 `FlexLayout` ，但单个属性实现 `AlignItems` 了相同的目标。 如果需要，可以使用附加的可 `AlignSelf` 绑定属性覆盖 `AlignItems` 各个项的属性：

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    进行此更改 `Label` `FlexLayout` 后，当读取顺序为从左到右时，此项将放置在的左边缘。

- [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent)属性的类型为 [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) ，指定如何在主轴上排列项。 `SpaceEvenly`选项在所有项之间、在第一项的上方以及最后一项的下方分配所有剩余的垂直空间。

    如果使用的是 `StackLayout` ，则需要将 `VerticalOptions` 每个项的属性分配给， `CenterAndExpand` 以实现类似的效果。 但是，此 `CenterAndExpand` 选项将在每个项之间分配两倍于第一项和最后一项之后的空间。 可以 `CenterAndExpand` `VerticalOptions` 通过将的 `JustifyContent` 属性设置 `FlexLayout` 为来模拟的选项 `SpaceAround` 。

下面将详细介绍 `FlexLayout` 可 **[绑定属性](#the-bindable-properties-in-detail)** 部分中的这些属性。

### <a name="using-flexlayout-for-wrapping-items"></a>使用 FlexLayout 包装项

**[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的 "**照片包装**" 页演示了如何将其子级 `FlexLayout` 包装到其他行或列。 XAML 文件实例化 `FlexLayout` 并分配它的两个属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

`Direction`未设置此的 `FlexLayout` 属性，因此它具有的默认设置 `Row` ，这意味着子项按行排列，并且主轴是水平轴。

[`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap)属性为枚举类型 [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) 。 如果行上容纳了太多的项，则此属性设置会导致项换行到下一行。

请注意， `FlexLayout` 是的子级 `ScrollView` 。 如果页面上容纳了太多行，则的 `ScrollView` 默认属性为， `Orientation` `Vertical` 并允许垂直滚动。

`JustifyContent`属性在水平轴 (的主轴上分配遗留空间) 以便将每个项放在相同空间量的周围。

代码隐藏文件可访问示例照片的集合，并将其添加到 `Children` 的集合中 `FlexLayout` ：

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

下面是正在运行的程序，从上到下逐渐滚动：

[![照片包装页](flex-layout-images/PhotoWrapping.png "照片包装页")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>具有 FlexLayout 的页面布局

Web 设计中有一个称为 " [_圣圣杯_](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) " 的标准布局，因为它是一种非常理想的布局格式，但通常很难实现。 布局由页面顶部的页眉和底部的页脚组成，并延伸到页面的整个宽度。 占据页面中心就是主要内容，但通常会在内容和补充信息的左侧出现纵栏式菜单 (有时会在右侧称为 _旁_) 。 [CSS 挠性 Box 布局规范的节 5.4.1](https://www.w3.org/TR/css-flexbox-1/#order-accessibility) 介绍了如何使用弹性框来实现圣圣杯布局。

**[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的 "**圣圣杯" 布局**页显示此布局的简单实现，该实现使用一个 `FlexLayout` 嵌套在另一个中的布局。 由于此页是针对纵向模式下的手机设计的，因此内容区域左侧和右侧的区域只有50像素宽：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

此处运行的是：

[![圣圣杯布局页](flex-layout-images/HolyGrailLayout.png "圣圣杯布局页")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

向左和向右呈现导航区域和搁置区域 `BoxView` 。

`FlexLayout`XAML 文件中的第一个具有垂直主轴，其中包含三个子元素，它们排列在一列中。 它们是页眉、页面的正文和页脚。 嵌套 `FlexLayout` 有一个水平主轴，其中有三个子轴排列在一行中。

此程序演示了三个附加的可绑定属性：

- `Order`附加的可绑定属性是在第一个中设置的 `BoxView` 。 此属性是一个整数，其默认值为0。 您可以使用此属性来更改布局顺序。 通常，开发人员更喜欢在导航项之前的标记中显示的页面内容。 将 `Order` 第一个属性的属性设置 `BoxView` 为小于其其他同级的值将导致它显示为行中的第一项。 同样，您可以通过将 `Order` 属性设置为大于同级的值来确保该项出现在最后。

- 为 `Basis` 两个项设置附加的可绑定属性 `BoxView` ，使其宽度为50像素。 此属性的类型为 `FlexBasis` ，该结构定义名为的类型的静态 `FlexBasis` 属性 `Auto` ，这是默认值。 您可以使用 `Basis` 指定一个像素大小或一个百分比，用于指示该项在主轴上所占的空间量。 它被称为 _基数_ ，因为它指定的项大小是所有后续布局的基础。

- 在 `Grow` 嵌套的 `Layout` 和表示内容的子上设置属性 `Label` 。 此属性的类型为 `float` ，其默认值为0。 如果设置为正值，则会将主轴上的所有剩余空间分配给该项目，并将其分配给具有正值的同级 `Grow` 。 空间按比例分配给值，与中的星型规范有些类似 `Grid` 。

    第一个 `Grow` 附加属性是在嵌套的上设置的 `FlexLayout` ，指示这 `FlexLayout` 是为了占用外部内所有未使用的垂直空间 `FlexLayout` 。 第二个 `Grow` 附加属性是在表示内容的上设置的 `Label` ，指示此内容将占用内部中所有未使用的水平空间 `FlexLayout` 。

    还有一个类似的 `Shrink` 附加可绑定属性，当子级的大小超过 `FlexLayout` 但不需要包装时，可以使用此属性。

### <a name="catalog-items-with-flexlayout"></a>具有 FlexLayout 的目录项

**[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例中的 "**目录项**" 页与["CSS 弹性布局" 对话框规范第1.1 节中的示例 1](https://www.w3.org//TR/css-flexbox-1/#overview)类似，不同之处在于它显示一系列水平滚动的图片和三个猴的说明：

[!["目录项" 页](flex-layout-images/CatalogItems.png ""目录项" 页")](flex-layout-images/CatalogItems-Large.png#lightbox)

这三个猴中的每一个都是 `FlexLayout` 包含在 `Frame` 给定了显式高度和宽度的中，并且也是更大的子级 `FlexLayout` 。 在此 XAML 文件中，子属性中的大多数属性 `FlexLayout` 都是在样式中指定的，但其中一个属性是隐式样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

的隐式样式 `Image` 包括两个附加的可绑定属性的设置 `Flexlayout` ：

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

如果 `Order` 设置为 &ndash; 1，则将在 `Image` 每个嵌套视图中首先显示元素， `FlexLayout` 而不考虑其在子集合中的位置。 的 `AlignSelf` 属性 `Center` 导致在 `Image` 内居中 `FlexLayout` 。 这会重写属性的设置 `AlignItems` ，该属性的默认值为 `Stretch` ，这意味着 `Label` 和 `Button` 子级拉伸到的整个宽度 `FlexLayout` 。

在这三个视图中的每个 `FlexLayout` 视图中，在之前为空， `Label` `Button` 但其 `Grow` 设置为1。 这意味着将所有额外的垂直空间分配给该空白 `Label` ，从而有效地将 `Button` 向下推送。

## <a name="the-bindable-properties-in-detail"></a>详细的可绑定属性

现在，你已了解了的一些常见应用程序 `FlexLayout` ， `FlexLayout` 可以更详细地探讨的属性。
`FlexLayout` 定义六个可绑定属性，这些属性在 `FlexLayout` 代码或 XAML 中设置为控件的方向和对齐方式。 本文未介绍 (其中一个属性 [`Position`](xref:Xamarin.Forms.FlexLayout.Position) 。 ) 

你可以使用**[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的**试验**页试验5个剩余的可绑定属性。 此页允许您在中添加或删除子级 `FlexLayout` ，并设置五个可绑定属性的组合。 的所有子级 `FlexLayout` 都是 `Label` 各种颜色和大小的视图，其 `Text` 属性设置为与在集合中的位置相对应的数字 `Children` 。

当程序启动时，五个 `Picker` 视图将显示这五个属性的默认值 `FlexLayout` 。 `FlexLayout`屏幕底部包含三个子元素：

[!["试验" 页：默认值](flex-layout-images/ExperimentDefault.png ""试验" 页-默认")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

每个 `Label` 视图都有一个灰色背景，其中显示分配给中的空间 `Label` `FlexLayout` 。 本身的背景 `FlexLayout` 为 Alice 蓝。 它占据页面的整个底部，但左侧和右侧有一个小边距。

### <a name="the-direction-property"></a>Direction 属性

[`Direction`](xref:Xamarin.Forms.FlexLayout.Direction)属性的类型为 [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) ，具有四个成员的枚举：

- `Column`
- `ColumnReverse` XAML 中的 (或 "column 反向") 
- `Row`（默认值）
- `RowReverse` XAML 中的 (或 "行反向") 

在 XAML 中，可以使用以小写、大写或混合大小写形式表示的枚举成员名称来指定此属性的值，也可以使用与 CSS 指示器相同的括号中显示的两个其他字符串。  (在 XAML 分析器使用的类中定义了 "列反转" 和 "行反向" 字符串 [`FlexDirectionTypeConverter`](xref:Xamarin.Forms.FlexDirectionTypeConverter) 。 ) 

下面是 " **试验** " 页，显示从左到右) 、 `Row` 方向、 `Column` 方向和 `ColumnReverse` 方向 (：

[![试验页：方向](flex-layout-images/ExperimentDirection.png "试验页方向")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

请注意，对于 `Reverse` 选项，项从右侧或底部开始。

### <a name="the-wrap-property"></a>Wrap 属性

[`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap)属性的类型为 [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) ，具有三个成员的枚举：

- `NoWrap`（默认值）
- `Wrap`
- `Reverse` XAML 中的 (或 "反向") 

从左到右，这些屏幕显示了 `NoWrap` `Wrap` 和 `Reverse` 12 个子项的选项：

[!["试验" 页：换行](flex-layout-images/ExperimentWrap.png "试验页换行")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

如果将 `Wrap` 属性设置为 `NoWrap` ，并且主轴被约束 (如此程序) 中所示，并且主轴的宽度或高度不足以容纳所有子级，则 `FlexLayout` 尝试使项目更小，如 iOS 屏幕截图所示。 可以用附加的可绑定属性控制项的 shrinkness [`Shrink`](#the-shrink-property) 。

### <a name="the-justifycontent-property"></a>JustifyContent 属性

[`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent)属性的类型为 [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) ，其中包含六个成员的枚举：

- `Start` XAML)  (或 "弹性启动"，默认值为
- `Center`
- `End` XAML 中的 (或 "flex end") 
- `SpaceBetween` XAML 中的 (或 "间隔") 
- `SpaceAround` XAML 中的 (或 "间隔") 
- `SpaceEvenly`

此属性指定如何在主轴上分隔项，这是本示例中的水平轴：

[![试验页：调整内容](flex-layout-images/ExperimentJustifyContent.png "试验页-调整内容")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

在所有三个屏幕截图中， `Wrap` 属性都设置为 `Wrap` 。 `Start`默认值在以前的 Android 屏幕截图中显示。 此处的 iOS 屏幕截图显示了 `Center` 选项：所有项都移到了中心。 以单词开头的三个其他选项 `Space` 会分配项未占用的额外空间。 `SpaceBetween` 在项之间平均分配空间;在每个项 `SpaceAround` 周围放置相等的空间，同时在 `SpaceEvenly` 每个项之间以及第一项和最后一项之后、第一项和最后一项之后。

### <a name="the-alignitems-property"></a>AlignItems 属性

[`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems)属性的类型为 [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) ，具有四个成员的枚举：

- `Stretch`（默认值）
- `Center`
- `Start` XAML 中的 (或 "flex start") 
- `End` XAML 中的 (或 "flex end") 

这是两个属性中的一种 () 另一种是 [`AlignContent`](#the-aligncontent-property) 指示子轴上的子项对齐方式的两个属性之一。 在每行中， (如前面的屏幕截图中所示) ，或在每个项的开始、居中或结尾处对齐，如以下三个屏幕截图中所示：

[!["试验" 页：对齐项](flex-layout-images/ExperimentAlignItems.png "试验页对齐项")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

在 iOS 屏幕截图中，所有子项的顶部均为对齐。 在 Android 屏幕截图中，项基于最高子级垂直居中。 在 UWP 屏幕截图中，所有项的底部均为对齐。

对于任何单个项，可将该设置替换为 `AlignItems` 附加的可 [`AlignSelf`](#the-alignself-property) 绑定属性。

### <a name="the-aligncontent-property"></a>AlignContent 属性

[`AlignContent`](xref:Xamarin.Forms.FlexLayout.AlignContent)属性的类型为 [`FlexAlignContent`](xref:Xamarin.Forms.FlexAlignContent) ，其中包含七个成员的枚举：

- `Stretch`（默认值）
- `Center`
- `Start` XAML 中的 (或 "flex start") 
- `End` XAML 中的 (或 "flex end") 
- `SpaceBetween` XAML 中的 (或 "间隔") 
- `SpaceAround` XAML 中的 (或 "间隔") 
- `SpaceEvenly`

与类似 `AlignItems` ， `AlignContent` 属性还会对齐跨轴上的子级，但会影响整行或整列：

[!["试验" 页：对齐内容](flex-layout-images/ExperimentAlignContent.png "试验页-对齐内容")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

在 iOS 屏幕截图中，这两行位于顶部;在 Android 屏幕截图中，它们位于中心;并在 UWP 屏幕截图中看到它们。 还可以通过多种方式分隔行：

[!["试验" 页：对齐内容2](flex-layout-images/ExperimentAlignContent2.png "试验页-对齐内容2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent`当只有一个行或列时，将不起作用。

## <a name="the-attached-bindable-properties-in-detail"></a>附加的可绑定属性详细信息

`FlexLayout` 定义五个附加的可绑定属性。 这些属性在的子级上设置 `FlexLayout` ，并且仅适用于该特定子级。

### <a name="the-alignself-property"></a>AlignSelf 属性

附加的可 [`AlignSelf`](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) 绑定属性的类型为 [`FlexAlignSelf`](xref:Xamarin.Forms.FlexAlignContent) ，这是一个包含五个成员的枚举：

- `Auto`（默认值）
- `Stretch`
- `Center`
- `Start` XAML 中的 (或 "flex start") 
- `End` XAML 中的 (或 "flex end") 

对于的任何单个子级 `FlexLayout` ，此属性设置将重写 [`AlignItems`](#the-alignitems-property) 自身上设置的属性 `FlexLayout` 。 的默认设置 `Auto` 表示使用 `AlignItems` 设置。

对于 `Label` 名为 `label` (或示例) 的元素，可以 `AlignSelf` 在代码中设置属性，如下所示：

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

请注意，不存在对的父级的引用 `FlexLayout` `Label` 。 在 XAML 中，按如下所示设置属性：

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Order 属性

[`Order`](xref:Xamarin.Forms.FlexLayout.OrderProperty)属性的类型为 `int` 。 默认值为 0。

`Order`属性允许您更改的子级 `FlexLayout` 排列顺序。 通常，的子级 `FlexLayout` 排列顺序与它们在集合中的显示顺序相同 `Children` 。 可以通过 `Order` 在一个或多个子级上将附加的可绑定属性设置为非零的整数值来覆盖此顺序。 `FlexLayout`然后，将基于每个子级上属性的设置来排列其子 `Order` 级，但具有相同设置的子级 `Order` 将按照它们在集合中出现的顺序排列 `Children` 。

### <a name="the-basis-property"></a>Basis 属性

附加的可 [`Basis`](xref:Xamarin.Forms.FlexLayout.BasisProperty) 绑定属性指示分配给 `FlexLayout` 主轴上的子级的空间量。 属性指定的大小 `Basis` 是沿父轴的大小 `FlexLayout` 。 因此， `Basis` 当子级按行排列时，或当子级按列排列时，表示子级的宽度。

`Basis`属性的类型为 [`FlexBasis`](xref:Xamarin.Forms.FlexBasis) ，结构。 可以在与设备无关的单位中指定大小，也可以指定大小的百分比 `FlexLayout` 。 属性的默认值 `Basis` 为静态属性 `FlexBasis.Auto` ，这意味着将使用子级请求的宽度或高度。

在代码中，可以设置 `Basis` `Label` 名 `label` 为40的与设备无关的设备的属性，如下所示：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

构造函数的第二个参数 `FlexBasis` 名为 `isRelative` ，指示大小是相对 (`true`) 还是绝对 (`false`) 。 自变量的默认值为 `false` ，因此还可以使用以下代码：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

定义了从到的隐式转换 `float` `FlexBasis` ，因此您可以进一步简化它：

```csharp
FlexLayout.SetBasis(label, 40);
```

可将大小设置为父项的25%， `FlexLayout` 如下所示：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

此小数值必须在0到1的范围内。

在 XAML 中，可以在与设备无关的单位中为大小使用数字：

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

或者，你可以指定介于0% 到100% 之间的百分比：

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的 "**基础实验**" 页允许您试验 `Basis` 属性。 此页将显示一个包含五个元素的包装的列， `Label` 并交替显示背景色和前景色。 可以通过两个 `Slider` 元素指定 `Basis` 第二个和第四个值 `Label` ：

[!["基本实验" 页](flex-layout-images/BasisExperiment.png ""基本实验" 页")](flex-layout-images/BasisExperiment-Large.png#lightbox)

左侧的 iOS 屏幕截图显示了两个 `Label` 元素在与设备无关的单位中的高度。 Android 屏幕显示它们的高度是的总高度的一小部分 `FlexLayout` 。 如果 `Basis` 设置为100%，则子级为的高度 `FlexLayout` ，并将换到下一列并占用该列的整个高度，因为 UWP 屏幕截图演示：它看起来就像这五个子级排列在一行中，但实际上是按5列排列的。

### <a name="the-grow-property"></a>增大属性

[`Grow`](xref:Xamarin.Forms.FlexLayout.GrowProperty)附加的可绑定属性的类型为 `int` 。 默认值为0，值必须大于或等于0。

`Grow`当 `Wrap` 属性设置为， `NoWrap` 并且子级的行的总宽度小于的宽度时，属性将起作用 `FlexLayout` ，或子级的列的高度小于 `FlexLayout` 。 `Grow`属性指示如何分配子级之间的遗留空间。

在 " **增长实验** " 页中，有五个 `Label` 交替颜色元素排列在一列中，两个 `Slider` 元素允许你调整 `Grow` 第二个和第四个属性 `Label` 。 最左侧的 iOS 屏幕截图显示默认 `Grow` 属性0：

[!["增长实验" 页](flex-layout-images/GrowExperiment.png ""增长实验" 页")](flex-layout-images/GrowExperiment-Large.png#lightbox)

如果为任何一个子级指定了正值 `Grow` ，则该子级将占用所有剩余空间，如 Android 屏幕截图所示。 此空间还可以在两个或多个子级中分配。 在 UWP 屏幕截图中， `Grow` 第二个属性的属性 `Label` 设置为0.5，而第四个的 `Grow` 属性为1.5，这将为第 `Label` `Label` 三次剩余空间赋予第二次剩余空间 `Label` 。

子视图使用该空间的方式取决于特定类型的子类型。 对于 `Label` ，可 `Label` 使用属性和在的总空间内放置文本 `HorizontalTextAlignment` `VerticalTextAlignment` 。

### <a name="the-shrink-property"></a>Shrink 属性

[`Shrink`](xref:Xamarin.Forms.FlexLayout.ShrinkProperty)附加的可绑定属性的类型为 `int` 。 默认值为1，值必须大于或等于0。

`Shrink`如果 `Wrap` 属性设置为， `NoWrap` 并且子级的行的聚合宽度大于的宽度 `FlexLayout` ，或子级的单个列的聚合高度大于的高度，则该属性将扮演角色 `FlexLayout` 。 通常， `FlexLayout` 将通过 constricting 其大小来显示这些子级。 `Shrink`属性可以指示哪些子级在按其完整大小显示时具有优先级。

" **收缩试验** " 页创建一个 `FlexLayout` ，该行中的五行 `Label` 需要比 width 更多的空间 `FlexLayout` 。 左侧的 iOS 屏幕截图显示 `Label` 默认值为1的所有元素：

[!["收缩试验" 页](flex-layout-images/ShrinkExperiment.png ""收缩试验" 页")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

在 Android 屏幕截图中， `Shrink` 第二个的值 `Label` 设置为0，并 `Label` 以其完整宽度显示。 此外，第四个 `Label` 值的 `Shrink` 值大于1，并收缩。 UWP 屏幕快照显示的两个 `Label` 元素的 `Shrink` 值均为0，以允许这些元素以其完整大小显示（如果可能）。

您可以设置 `Grow` 和值， `Shrink` 以适应聚合子大小有时可能小于或大于的大小的情况 `FlexLayout` 。

## <a name="css-styling-with-flexlayout"></a>具有 FlexLayout 的 CSS 样式

您可以使用随3.0 一起引入的 [CSS 样式](~/xamarin-forms/user-interface/styles/css/index.md) 功能 Xamarin.Forms 与进行连接 `FlexLayout` 。 **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的 " **CSS 目录项**" 页复制 "**目录项**" 页的布局，但包含许多样式的 css 样式表：

[!["CSS 目录项" 页](flex-layout-images/CssCatalogItems.png ""CSS 目录项" 页")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

原始 **CatalogItemsPage** 文件的 `Style` 部分中具有 `Resources` 15 个 `Setter` 对象。 在 **CssCatalogItemsPage** 文件中，已减少为 `Style` 仅包含四个对象的两个定义 `Setter` 。 以下样式对 Xamarin.Forms css 样式功能当前不支持的属性的 CSS 样式表进行了补充：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

在部分的第一行中引用了 CSS 样式表 `Resources` ：

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

另请注意，这三个项目中的每个元素都包括 `StyleClass` 设置：

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

这些是指 **CatalogItemsStyles** 样式表中的选择器：

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

`FlexLayout`此处引用了几个附加的可绑定属性。 在 `label.empty` 选择器中，会看到 `flex-grow` 属性，该属性的样式为空 `Label` 以提供上面的一些空白区域 `Button` 。 `image`选择器包含 `order` 特性和 `align-self` 特性，两者都对应于附加的可 `FlexLayout` 绑定属性。

你已看到，你可以直接在上设置属性 `FlexLayout` ，并且可以在的子级上设置附加的可绑定属性 `FlexLayout` 。 或者，可以使用传统的基于 XAML 的样式或 CSS 样式间接设置这些属性。 重要的是了解并了解这些属性。 这些属性是 `FlexLayout` 真正灵活的。

## <a name="flexlayout-with-xamarinuniversity"></a>用 Xamarin 大学 FlexLayout

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Forms 3.0 Flex 布局视频**

## <a name="related-links"></a>相关链接

- [FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)