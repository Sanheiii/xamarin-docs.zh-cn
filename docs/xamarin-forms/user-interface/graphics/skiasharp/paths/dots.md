---
title: SkiaSharp 中的点和短划线
description: 本文探讨了如何在 SkiaSharp 中掌握绘制虚线和虚线的复杂性，并通过示例代码对此进行了演示。
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5064a53b140c26acdc5149f5495cc002e657a9b0
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563999"
---
# <a name="dots-and-dashes-in-skiasharp"></a>SkiaSharp 中的点和短划线

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_掌握 SkiaSharp 中绘制点线和虚线的复杂性_

SkiaSharp 使你能够绘制不是纯色的行，而是由点和短划线组成：

![虚线](dots-images/dottedlinesample.png)

使用路径效果执行此操作， *路径效果*是 [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) 您设置为的属性的类的实例 [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) `SKPaint` 。 您可以使用由定义的一种静态创建方法)  (或组合路径效果来创建路径效果 `SKPathEffect` 。  (`SKPathEffect` 是 SkiaSharp 支持的六种效果之一; 其他是在 [**SkiaSharp 效果**](../effects/index.md)部分中介绍的。 ) 

若要绘制点线或虚线，请使用 [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) 静态方法。 有两个参数：第一个是值的数组 `float` ，这些值指示点和短划线的长度以及它们之间的空格长度。 此数组必须具有偶数个元素，并且应该至少有两个元素。  (数组中可以有零个元素，但这会导致实线。 ) 如果有两个元素，则第一个元素是点或短划线的长度，第二个是下一个点或短划线之前的间距长度。 如果有两个以上的元素，则其顺序如下：虚线长度、间隙长度、短划线长度、间隙长度等。

通常，您需要使虚线和间隙长度为笔划宽度的倍数。 例如，如果笔划宽度为10像素，则数组 {10，10} 将绘制虚线，其中点和间隙与笔划粗细的长度相同。

但是， `StrokeCap` 对象的设置 `SKPaint` 也会影响这些点和短划线。 正如您很快将看到的那样，这将影响此数组的元素。

**点**划线和虚线页上演示了虚线和虚线。 [**DotsAndDashesPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml)文件实例化两个 `Picker` 视图，一个视图用于选择笔划帽，第二个视图用于选择虚线数组：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>10, 10</x:String>
                    <x:String>30, 10</x:String>
                    <x:String>10, 10, 30, 10</x:String>
                    <x:String>0, 20</x:String>
                    <x:String>20, 20</x:String>
                    <x:String>0, 20, 20, 20</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

 `dashArrayPicker`假设笔划宽度为10像素，则中的前三个项。 {10，10} 数组适用于虚线，{30，10} 适用于虚线，而 {10，10，30，10} 适用于点-短划线。 稍后将讨论其他三个 (。 ) 

[`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs)代码隐藏文件包含 `PaintSurface` 用于访问视图的事件处理程序和几个帮助器例程 `Picker` ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem,
        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string str = (string)picker.SelectedItem;
    string[] strs = str.Split(new char[] { ' ', ',' }, StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

在下面的屏幕截图中，最左侧的 iOS 屏幕显示一条虚线：

[![点和短划线页面的三向屏幕截图](dots-images/dotsanddashes-small.png)](dots-images/dotsanddashes-large.png#lightbox "点和短划线页面的三向屏幕截图")

不过，Android 屏幕也应该使用数组 {10，10} 显示一条虚线，而不是直线。 发生了什么情况？ 问题在于 Android 屏幕还具有的笔划帽设置 `Square` 。 这会将所有短划线扩展为笔划宽度的一半，使其填满空白。

若要在使用或的笔划帽时避开此 `Square` 问题 `Round` ，必须将数组中的短划线长度减小 (笔划长度，这有时会导致虚线长度为 0) ，并按笔划长度增加间隙长度。 这就是 XAML 文件中最后三个短划线数组的 `Picker` 计算方式：

- 对于虚线，{10，10} 变为 {0，20}
- 对于虚线，{30，10} 变为 {20，20}
- 对于点线和虚线，{10，10，30，10} 变为 {0、20、20、20}

UWP 屏幕显示的笔划帽的虚线和虚线 `Round` 。 `Round`笔划帽通常在粗线中提供点和短划线的最佳外观。

到目前为止，并未提及方法的第二个参数 `SKPathEffect.CreateDash` 。 此参数名为 `phase` ，它引用线条开头的点-短划线模式内的偏移量。 例如，如果短划线数组为 {10，10} 并且 `phase` 为10，则该行以间隙（而不是点）开头。

动画中有一个有趣的 `phase` 参数应用。 **动画螺旋线**页面类似于**Archimedean 螺旋线**页面，只不过 [`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) 类使用方法对参数进行动画处理 `phase` Xamarin.Forms `Device.Timer` ：

```csharp
public class AnimatedSpiralPage : ContentPage
{
    const double cycleTime = 250;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float dashPhase;

    public AnimatedSpiralPage()
    {
        Title = "Animated Spiral";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            dashPhase = (float)(10 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }
    ···  
}
```

当然，您必须实际运行该程序才能看到动画：

[![动画螺旋线页面的三向屏幕截图](dots-images/animatedspiral-small.png)](dots-images/animatedspiral-large.png#lightbox "动画螺旋线页面的三向屏幕截图")

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (示例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)