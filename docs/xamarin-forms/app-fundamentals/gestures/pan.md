---
title: 添加平移手势识别器
description: 本文说明如何使用平移手势水平和垂直平移图像，以便在小于图像尺寸的视区中显示图像内容时，可以查看所有图像内容。
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c9968ac7de45f3e6e239f82ba43b53abd05a647
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558513"
---
# <a name="add-a-pan-gesture-recognizer"></a>添加平移手势识别器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_平移手势用于检测手指在屏幕上的移动并将该移动应用于内容，可通过 `PanGestureRecognizer` 类实现。平移手势的常见使用场景是水平和垂直平移图像，以便在小于图像尺寸的视区中显示图像内容时，可以查看所有图像内容。这是通过在视区内移动图像来实现的，本文将对此进行演示。_

若要让某个用户界面元素可以使用平移手势进行移动，请创建 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 实例，处理 [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) 事件，并将新的手势识别器添加到该用户界面元素上的 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 集合。 以下代码示例展示了附加到 [`Image`](xref:Xamarin.Forms.Image) 元素的 `PanGestureRecognizer`：

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

这也可以在 XAML 中实现，如下面的代码示例所示：

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

然后将 `OnPanUpdated` 事件处理程序的代码添加到代码隐藏文件中：

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

## <a name="creating-a-pan-container"></a>创建平移容器

本部分包含可执行任意形式的平移的通用帮助程序类，该类通常适用于在图像或地图中导航。 通过处理平移手势来执行此操作时，需要使用某种数学运算来转换用户界面。 此数学运算用于仅在包装的用户界面元素的边界内进行平移。 以下代码示例演示 `PanContainer` 类：

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

此类可以包装用户界面元素，以便平移手势平移包装的用户界面元素。 以下 XAML 代码示例介绍用于包装 [`Image`](xref:Xamarin.Forms.Image) 元素的 `PanContainer`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

以下代码示例展示了 `PanContainer` 如何在 C# 页面中包装 [`Image`](xref:Xamarin.Forms.Image) 元素：

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

在这两个示例中，[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 属性设置为正在显示的图像的宽度值和高度值。

当 [`Image`](xref:Xamarin.Forms.Image) 元素接收到平移手势时，将平移显示的图像。 平移由 `PanContainer.OnPanUpdated` 方法执行，如以下代码示例所示：

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

此方法根据用户的平移手势来更新包装的用户界面元素的可查看内容。 这是通过以下方式来实现的：使用 [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) 实例的 [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) 和 [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) 属性的值来计算平移的方向和距离。 `App.ScreenWidth` 和 `App.ScreenHeight` 属性提供视区的高度和宽度，并通过特定于平台的相应项目设置为设备的屏幕宽度值和屏幕高度值。 然后，通过将包装的用户元素的 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 属性设置为已计算值，对该元素进行平移。

对未占据整个屏幕的元素中的内容进行平移时，可以从该元素的 [`Height`](xref:Xamarin.Forms.VisualElement.Height) 和 [`Width`](xref:Xamarin.Forms.VisualElement.Width) 属性中获取视区的高度和宽度。

> [!NOTE]
> 显示高分辨率图像可能会极大地增加应用的内存占用量。 因此，应仅在需要时创建此类图像，并且当应用不再需要它们时，应立即将其释放。 有关详细信息，请参阅[优化图像资源](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources)。

## <a name="related-links"></a>相关链接

- [PanGesture（示例）](/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)