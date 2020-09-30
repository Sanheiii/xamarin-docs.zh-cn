---
title: Xamarin.Forms BoxView
description: 本文介绍如何在应用程序中使用有色矩形来实现修饰、图形和交互 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3fba761d2740f323fb55e80d8cb4e3ba3d8e7cad
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563596"
---
# <a name="no-locxamarinforms-boxview"></a>Xamarin.Forms BoxView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)

[`BoxView`](xref:Xamarin.Forms.BoxView) 呈现指定的宽度、高度和颜色的简单矩形。 你可以使用 `BoxView` 来装饰、基本图形以及通过触摸来与用户交互。

因为没有 Xamarin.Forms 内置矢量图形系统，所以 `BoxView` 有助于进行补偿。 本文中介绍的一些示例程序用于 `BoxView` 呈现图形。 `BoxView`可以调整大小，使其类似于特定宽度和粗细的线条，然后使用属性旋转任意角度 `Rotation` 。

尽管 `BoxView` 可以模拟简单图形，但你可能希望[在中 Xamarin.Forms 使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)进行调查，以获得更复杂的图形要求。

## <a name="setting-boxview-color-and-size"></a>设置 BoxView 颜色和大小

通常会设置以下属性 `BoxView` ：

- [`Color`](xref:Xamarin.Forms.BoxView.Color) 设置其颜色。
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius) 设置其角半径。
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 设置的宽度（以与 `BoxView` 设备无关的单位）。
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 设置的高度 `BoxView` 。

`Color`属性的类型为 `Color` ; 可将属性设置为任何 `Color` 值，包括命名颜色的141静态只读字段，范围从 `AliceBlue` 到到 `YellowGreen` 。

`CornerRadius`属性的类型为 [`CornerRadius`](xref:Xamarin.Forms.CornerRadius) ; 属性可以设置为单个 `double` 统一角半径值，也可以设置为 `CornerRadius` 由四个值定义的结构，该结构应用于左上角、 `double` 右上方、左下方和右下方 `BoxView` 。

`WidthRequest`如果在 `HeightRequest` `BoxView` 布局中不受*约束*，则和属性仅扮演角色。 如果布局容器需要知道子元素的大小，例如，当 `BoxView` 是布局中的自动调整单元格的子元素时，就会出现这种情况 `Grid` 。 `BoxView`如果其 `HorizontalOptions` 和 `VerticalOptions` 属性设置为以外的值，则也不受约束 `LayoutOptions.Fill` 。 如果 `BoxView` 无限制，但 `WidthRequest` `HeightRequest` 未设置和属性，则会将宽度或高度设置为默认值40单位，或移动设备上约1/4 英寸。

`WidthRequest`如果在 `HeightRequest` 布局中受到约束，则将忽略和属性 `BoxView` ，在这种情况下，布局容器在上施加自己的大小*constrained* `BoxView` 。

`BoxView` 可以在一个维度上受约束，而在另一个维度上不受约束。 例如，如果 `BoxView` 是垂直的子级，则的 `StackLayout` 垂直尺寸 `BoxView` 是不受限制的，并且其水平尺寸通常受到约束。 但这种水平维度存在异常：如果将 `BoxView` 其 `HorizontalOptions` 属性设置为以外的其他内容 `LayoutOptions.Fill` ，则水平维度也不受约束。 本身也可能 `StackLayout` 具有一个不受约束的水平维度，在这种情况下，也可能会不 `BoxView` 受限制。

[**BasicBoxView**](/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)示例 `BoxView` 在其页面中心显示一英寸的不受限制的块：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             CornerRadius="10"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

结果如下：

[![基本 BoxView](boxview-images/basicboxview-small.png "基本 BoxView")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

如果 `VerticalOptions` 和 `HorizontalOptions` 属性已从标记中删除 `BoxView` 或者设置为 `Fill` ，则将 `BoxView` 被页大小约束，并扩展以填充页。

`BoxView`也可以是的子级 `AbsoluteLayout` 。 在这种情况下， `BoxView` 使用附加的可绑定属性设置的位置和大小 `LayoutBounds` 。 `AbsoluteLayout`文章[**AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolutelayout.md)中对进行了讨论。

在下面的示例程序中，你将看到所有这些事例的示例。

## <a name="rendering-text-decorations"></a>呈现文本修饰

您可以使用在 `BoxView` 页面上添加一些简单的装饰，并以水平线和竖线的形式。 [**TextDecoration**](/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)示例演示了这一点。 程序的所有视觉对象都在 **MainPage** 文件中定义，该文件包含 `Label` `BoxView` 以下内容中的多个和元素 `StackLayout` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

下面的所有标记都是的子级 `StackLayout` 。 此标记包含用于元素的多种类型的修饰 `BoxView` 元素 `Label` ：

[![文本修饰](boxview-images/textdecoration-small.png "文本修饰")](boxview-images/textdecoration-large.png#lightbox "文本修饰")

页面顶部的 "时尚" 标头可通过 `AbsoluteLayout` ，其子元素是四个 `BoxView` 元素和 a `Label` ，所有这些元素都分配有特定的位置和大小：

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

在 XAML 文件中， `AbsoluteLayout` 后面是 `Label` 带有用于描述的格式化文本的 `AbsoluteLayout` 。

可以通过 `Label` 将和的 `BoxView` `StackLayout` `HorizontalOptions` 值设置为以外的值， `Fill` 将文本字符串用下划线表示。 的宽度由的 `StackLayout` 宽度控制 `Label` ，然后在上强加该宽度 `BoxView` 。 `BoxView`只为分配一个显式高度：

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

不能使用此方法为较长的文本字符串或段落中的单个单词加下划线。

还可以使用 `BoxView` 来与 HTML `hr` (水平标尺) 元素类似。 只需让的宽度 `BoxView` 由其父容器确定，在本例中为 `StackLayout` ：

```xaml
<BoxView HeightRequest="3" />
```

最后，你可以通过将 `BoxView` 和 `Label` 置于水平中来在文本段落的一侧绘制竖线 `StackLayout` 。 在这种情况下，的高度与的高度 `BoxView` 相同 `StackLayout` ，后者由的高度控制 `Label` ：

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```

## <a name="listing-colors-with-boxview"></a>用 BoxView 列出颜色

`BoxView`对于显示颜色非常方便。 此程序使用 `ListView` 来列出结构的所有公共静态只读字段 Xamarin.Forms `Color` ：

[![ListView 颜色](boxview-images/listviewcolors-small.png "ListView 颜色")](boxview-images/listviewcolors-large.png#lightbox "ListView 颜色")

[**ListViewColors**](/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors)程序包含一个名为的类 `NamedColor` 。 静态构造函数使用反射来访问结构的所有字段 `Color` ，并 `NamedColor` 为每个字段创建一个对象。 它们存储在静态属性中 `All` ：

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

XAML 文件中描述了程序视觉对象。 `ItemsSource`的属性 `ListView` 设置为 static `NamedColor.All` 属性，这意味着将 `ListView` 显示所有单个 `NamedColor` 对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

`NamedColor`对象由 `ViewCell` 设置为的数据模板的对象进行格式设置 `ListView` 。 此模板包括 `BoxView` 其 `Color` 属性绑定到 `Color` 对象的属性的 `NamedColor` 。

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>通过对 BoxView 进行分类来玩生活游戏

生活的游戏是 mathematician John Conway 和那时推广在七十年代的 *美国科研* 页中 John 和的移动自动机。 维基百科文章 [Conway](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)提供了一个很好的简介。

Xamarin.Forms [**GameOfLife**](/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)程序定义了一个 `LifeCell` 派生自的名为的类 `BoxView` 。 此类封装了生活中单个单元的逻辑：

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell` 向添加了其他三个属性 `BoxView` ： `Col` 和 `Row` 属性存储单元格在网格中的位置， `IsAlive` 属性指示其状态。 `IsAlive` `Color` `BoxView` 如果单元格处于活动状态，则该属性还会将的属性设置为黑色; 如果单元格不处于活动状态，则将设置为白色。

`LifeCell` 还会安装 `TapGestureRecognizer` ，以允许用户通过点击来切换单元的状态。 类将 `Tapped` 事件从手势识别器转换为其自己的 `Tapped` 事件。

**GameOfLife**程序还包括一个 `LifeGrid` 类，该类封装游戏的很多逻辑，以及一个 `MainPage` 处理程序视觉对象的类。 其中包括描述游戏规则的覆盖区。 下面是在 `LifeCell` 页面上显示几百个对象的程序：

[![生活游戏](boxview-images/gameoflife-small.png "生活游戏")](boxview-images/gameoflife-large.png#lightbox "生活游戏")

## <a name="creating-a-digital-clock"></a>创建数字时钟

[**DotMatrixClock**](/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)程序创建了 210 `BoxView` 个元素，用于模拟老式 5 x 7 点矩阵显示的点。 您可以在纵向或横向模式下读取时间，但在横向看起来更大：

[![点阵时钟](boxview-images/dotmatrixclock-small.png "点阵时钟")](boxview-images/dotmatrixclock-large.png#lightbox "点阵时钟")

XAML 文件不只是实例化 `AbsoluteLayout` 用于时钟的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

其他所有内容都出现在代码隐藏文件中。 通过定义多个数组的定义来大大简化点矩阵显示逻辑，这些数组描述了每个10位数和冒号对应的点：

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

这些字段将结束，其中包含一个三维元素数组， `BoxView` 用于存储六个数字的点模式。

构造函数将创建 `BoxView` 数字和冒号的所有元素，还会初始化冒号的 `Color` 元素的属性 `BoxView` ：

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

此程序使用的相对定位和调整大小功能 `AbsoluteLayout` 。 每个的宽度和高度 `BoxView` 均设置为小数值，具体而言，85%/1 除以水平点和垂直点的数量。 位置也设置为小数值。

由于所有位置和大小都与的总大小相关 `AbsoluteLayout` ，因此 `SizeChanged` 页面的处理程序只需设置的 `HeightRequest` `AbsoluteLayout` ：

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

的宽度会 `AbsoluteLayout` 自动设置，因为它拉伸到页面的整个宽度。

类中的最终代码 `MainPage` 处理计时器回调并着色每个数字的点。 代码隐藏文件开头的多维数组定义可帮助将此逻辑作为程序的最简单部分：

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```

## <a name="creating-an-analog-clock"></a>创建模拟时钟

点阵时钟似乎是一个明显的应用程序 `BoxView` ，但 `BoxView` 元素也能够实现模拟时钟：

[![BoxView 时钟](boxview-images/boxviewclock-small.png "BoxView 时钟")](boxview-images/boxviewclock-large.png#lightbox "BoxView 时钟")

[**BoxViewClock**](/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)程序中的所有视觉对象都是的子级 `AbsoluteLayout` 。 这些元素使用 `LayoutBounds` 附加属性进行调整，并使用属性进行旋转 `Rotation` 。

`BoxView`在 XAML 文件中实例化时钟的三个元素，但不会对其进行定位或调整大小：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

代码隐藏文件的构造函数实例化 `BoxView` 时钟圆周上刻度线的60元素：

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

所有元素的大小和位置都 `BoxView` 发生在 `SizeChanged` 的处理程序中 `AbsoluteLayout` 。 类的内部的一个内部结构称为，它 `HandParams` 相对于时钟的总大小描述三个指针的大小：

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

`SizeChanged`处理程序确定的中心和半径 `AbsoluteLayout` ，然后将60元素的大小和位置调整 `BoxView` 为刻度线。 `for`循环通过设置 `Rotation` 每个元素的属性来结束 `BoxView` 。 `SizeChanged`处理程序结束时， `LayoutHand` 会调用方法来调整时钟的大小和位置：

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

`LayoutHand`方法的大小和位置分别指向12:00 位置。 在方法结束时， `AnchorY` 属性设置为与时钟中心相对应的位置。 这表示旋转中心。

在计时器回调函数中旋转指针：

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

第二种处理方式略有不同：应用动画缓动函数以使移动看起来不是平滑。 每次计时周期后，第二次就会回发，然后超限其目标。 这小段代码在移动的真实感上增加了很多。

## <a name="related-links"></a>相关链接

- [基本 BoxView (示例) ](/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)
- [文本修饰 (示例) ](/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)
- [ListView 颜色 (示例) ](/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)
- [生活 (示例) ](/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)
- [点阵时钟 (示例) ](/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)
- [BoxView 时钟 (示例) ](/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)
- [BoxView](xref:Xamarin.Forms.BoxView)