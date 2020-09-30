---
title: Xamarin.Forms 期限
description: Xamarin.FormsFrame 类是一种布局，用于使用可以配置为颜色、阴影和其他选项的边框来包装视图或布局。
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a14390afc04c1a0b2ccb5199350b69a9645a794
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563544"
---
# <a name="no-locxamarinforms-frame"></a>Xamarin.Forms 期限

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

Xamarin.Forms [`Frame`](xref:Xamarin.Forms.Frame) 类是一种布局，用于使用可以配置颜色、阴影和其他选项的边框来包装视图。 框架通常用于在控件周围创建边框，但可用于创建更复杂的 UI。 有关详细信息，请参阅 [高级帧使用](#advanced-frame-usage)。

以下屏幕截图显示了 `Frame` iOS 和 Android 上的控件：

[!["IOS 和 Android 上的框架示例"](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "IOS 和 Android 上的框架示例")

`Frame` 类定义了以下属性：

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor)`Color`确定边框颜色的值 `Frame` 。
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)`float`确定角的圆角半径的值。
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)`bool`用于确定帧是否有投影的值。

这些属性是由对象支持的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，这意味着 `Frame` 可以是数据绑定的目标。

> [!NOTE]
> `HasShadow`属性行为与平台相关。 默认值为 `true` "所有平台"。 但是，不会呈现 UWP 投影。 投影在 Android 和 iOS 上呈现，但 iOS 上的投影较暗，占用更多空间。

## <a name="create-a-frame"></a>创建框架

`Frame`可以在 XAML 中实例化。 默认 `Frame` 对象具有白色背景、投影和无边框。 `Frame`对象通常包装另一个控件。 下面的示例演示默认 `Frame` 包装 `Label` 对象：

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

`Frame`也可以在代码中创建：

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

`Frame` 通过在 XAML 中设置属性，可以自定义对象的圆角、着色边框和投影。 下面的示例演示一个自定义 `Frame` 对象：

```xaml
<Frame BorderColor="Orange"
       CornerRadius="10"
       HasShadow="True">
  <Label Text="Example" />
</Frame>
```

还可以在代码中设置这些实例属性：

```csharp
Frame frame = new Frame
{
    BorderColor = Color.Orange,
    CornerRadius = 10,
    HasShadow = true,
    Content = new Label { Text = "Example" }
};
```

## <a name="advanced-frame-usage"></a>高级帧使用情况

`Frame`类继承自 `ContentView` ，这意味着它可以包含任何类型的 `View` 对象（包括 `Layout` 对象）。 此功能允许 `Frame` 使用来创建复杂的 UI 对象，如卡。

### <a name="create-a-card-with-a-frame"></a>创建带有帧的卡片

将 `Frame` 对象与对象 `Layout` （如对象）相结合 `StackLayout` ，可以创建更复杂的 UI。 以下屏幕截图显示了使用对象创建的示例卡 `Frame` ：

[!["使用框架创建的卡片的屏幕截图"](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "使用框架创建的卡片的屏幕截图")

以下 XAML 演示了如何使用类创建卡片 `Frame` ：

```xaml
<Frame BorderColor="Gray"
       CornerRadius="5"
       Padding="8">
  <StackLayout>
    <Label Text="Card Example"
           FontSize="Medium"
           FontAttributes="Bold" />
    <BoxView Color="Gray"
             HeightRequest="2"
             HorizontalOptions="Fill" />
    <Label Text="Frames can wrap more complex layouts to create more complex UI components, such as this card!"/>
  </StackLayout>
</Frame>
```

还可以在代码中创建卡片：

```csharp
Frame cardFrame = new Frame
{
    BorderColor = Color.Gray,
    CornerRadius = 5,
    Padding = 8,
    Content = new StackLayout
    {
        Children =
        {
            new Label
            {
                Text = "Card Example",
                FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label)),
                FontAttributes = FontAttributes.Bold
            },
            new BoxView
            {
                Color = Color.Gray,
                HeightRequest = 2,
                HorizontalOptions = LayoutOptions.Fill
            },
            new Label
            {
                Text = "Frames can wrap more complex layouts to create more complex UI components, such as this card!"
            }
        }
    }
};
```

### <a name="round-elements"></a>Round 元素

`CornerRadius`控件的属性 `Frame` 可用于创建圆形图像。 以下屏幕截图显示了一个使用对象创建的圆形图像示例 `Frame` ：

[!["使用框架创建的圆形图像的屏幕截图"](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "使用框架创建的圆形图像的屏幕截图")

以下 XAML 演示了如何在 XAML 中创建圆形图像：

```xaml
<Frame Margin="10"
       BorderColor="Black"
       CornerRadius="50"
       HeightRequest="60"
       WidthRequest="60"
       IsClippedToBounds="True"
       HorizontalOptions="Center"
       VerticalOptions="Center">
  <Image Source="outdoors.jpg"
         Aspect="AspectFill"
         Margin="-20"
         HeightRequest="100"
         WidthRequest="100" />
</Frame>
```

还可以在代码中创建圆圈图像：

```csharp
Frame circleImageFrame = new Frame
{
    Margin = 10,
    BorderColor = Color.Black,
    CornerRadius = 50,
    HeightRequest = 60,
    WidthRequest = 60,
    IsClippedToBounds = true,
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center,
    Content = new Image
    {
        Source = ImageSource.FromFile("outdoors.jpg"),
        Aspect = Aspect.AspectFill,
        Margin = -20,
        HeightRequest = 100,
        WidthRequest = 100
    }
};
```

必须将 **outdoors.jpg** 映像添加到每个平台项目中，此操作的实现方式因平台而异。 有关详细信息，请参阅[中 Xamarin.Forms 的图像](~/xamarin-forms/user-interface/images.md)。

> [!NOTE]
> 圆角在各种平台上的行为略有不同。 `Image`对象的长度 `Margin` 应为图像宽度和父框架宽度之间的一半，并且应为负数以使图像在对象内均匀对齐 `Frame` 。 但是，不能保证请求的宽度和高度，因此 `Margin` ， `HeightRequest` 和 `WidthRequest` 属性可能需要根据映像大小和其他布局选项进行更改。

## <a name="related-links"></a>相关链接

* [帧演示](/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [中的映像 Xamarin.Forms](~/xamarin-forms/user-interface/images.md)